内存池

## 一、nginx内存管理简介

Nginx的内存管理是通过内存池来实现的。

## 二、数据结构定义

```c
/**
 * Nginx 内存池数据结构
**/
typedef struct ngx_pool_s            ngx_pool_t;
struct ngx_pool_s {
    ngx_pool_data_t       d; 		/* 内存池的数据区域*/
    size_t                max; 		/* 最大每次可分配内存 */
    ngx_pool_t           *current;  /* 指向当前的内存池指针地址。ngx_pool_t链表上最后一个缓存池结构*/
    ngx_chain_t          *chain;	/* 缓冲区链表 ngx_buf.h*/
    ngx_pool_large_t     *large;    /* 存储大数据的链表 */
    ngx_pool_cleanup_t   *cleanup;  /* 可自定义回调函数，清除内存块分配的内存 */
    ngx_log_t            *log;      /* 日志 ngx_log.h*/
};
```

```c
// 数据区域结构
typedef struct {
    u_char               *last;  /* 内存池中未使用内存的开始节点地址 */
    u_char               *end;   /* 内存池的结束地址 */
    ngx_pool_t           *next;  /* 指向下一个内存池 */
    ngx_uint_t            failed;/* 失败次数 */
} ngx_pool_data_t;

// 大数据块结构
typedef struct ngx_pool_large_s  ngx_pool_large_t;
struct ngx_pool_large_s {
    ngx_pool_large_t     *next;   /* 指向下一个存储地址 通过这个地址可以知道当前块长度 */
    void                 *alloc;  /* 数据块指针地址 */
};

// 自定义清理回调的数据结构
typedef void (*ngx_pool_cleanup_pt)(void *data);
typedef struct ngx_pool_cleanup_s  ngx_pool_cleanup_t;
struct ngx_pool_cleanup_s {
    ngx_pool_cleanup_pt   handler;
    void                 *data;
    ngx_pool_cleanup_t   *next;
};
```

## 三、数据结构图

![ngx_palloc_1](.\core_image\ngx_palloc_1.png)

## 四、内存池基本操作

内存池对外的主要方法有：

| 函数                                                         | 说明                                               |
| ------------------------------------------------------------ | -------------------------------------------------- |
| ngx_pool_t * ngx_create_pool(size_t size, ngx_log_t *log);   | 创建内存池                                         |
| void ngx_destroy_pool(ngx_pool_t *pool);                     | 销毁内存池                                         |
| void ngx_reset_pool(ngx_pool_t *pool);                       | 重置内存池                                         |
| void * ngx_palloc(ngx_pool_t *pool, size_t size);            | 从内存池分配内存（对齐）                           |
| void * ngx_pnalloc(ngx_pool_t *pool, size_t size);           | 从内存池分配内存（清零，不对齐）                   |
| void * ngx_pcalloc(ngx_pool_t *pool, size_t size);           | 从内存池分配内存（清零，对齐）                     |
| void * ngx_pmemalign(ngx_pool_t *pool, size_t size, size_t alignment) | 直接向操作系统中申请内存，进行对齐                 |
| static ngx_inline void * ngx_palloc_small(ngx_pool_t *pool, size_t size, ngx_uint_t align) | 直接向操作系统中申请内存，分配小块内存             |
| static void * ngx_palloc_block(ngx_pool_t *pool, size_t size) | 创建一个内存池节点                                 |
| static void * ngx_palloc_large(ngx_pool_t *pool, size_t size) | 直接向操作系统中申请内存，分配大块内存             |
| ngx_int_t ngx_pfree(ngx_pool_t *pool, void *p);              | 大块内存的释放                                     |
| ngx_pool_cleanup_t * ngx_pool_cleanup_add(ngx_pool_t *p, size_t size) | 添加一个需要在释放内存池时同步释放资源时调用的函数 |
| void ngx_pool_run_cleanup_file(ngx_pool_t *p, ngx_fd_t fd)   | 执行ngx_pool_cleanup_file函数                      |
| void ngx_pool_cleanup_file(void *data)                       | 缺省提供的一个以关闭文件来释放资源的方法           |
| void ngx_pool_delete_file(void *data)                        | 缺省提供的一个以删除文件来释放资源的方法           |

在分析内存池方法前，需要对几个主要的内存相关函数作一下介绍（见：./src/Os/Unix（Win32）/ngx_alloc.h/.c）

这里仅对Win32的作介绍：

```
// ngx_alloc（只是对malloc进行了简单的封装）
void *ngx_alloc(size_t size, ngx_log_t *log)
{
    void  *p;

    p = malloc(size);
    if (p == NULL) {
        ngx_log_error(NGX_LOG_EMERG, log, ngx_errno,
                      "malloc(%uz) failed", size);
    }

    ngx_log_debug2(NGX_LOG_DEBUG_ALLOC, log, 0, "malloc: %p:%uz", p, size);

    return p;
}

// 调用malloc并初始化为0
void *ngx_calloc(size_t size, ngx_log_t *log)
{
    void  *p;

    p = ngx_alloc(size, log);

    if (p) {
        ngx_memzero(p, size);
    }

    return p;
}

// ngx_memzero
#define ngx_memzero(buf, n)       (void) memset(buf, 0, n)

// ngx_free 
#define ngx_free          free  

// ngx_memalign
#define ngx_memalign(alignment, size, log)  ngx_alloc(size, log)
```

## 四、函数具体实现

1.创建内存池

```c
// 创建一个内存池
ngx_pool_t *
ngx_create_pool(size_t size, ngx_log_t *log)
{
    ngx_pool_t  *p;

    p = ngx_memalign(NGX_POOL_ALIGNMENT, size, log);
    if (p == NULL) {
        return NULL;
    }

    p->d.last = (u_char *) p + sizeof(ngx_pool_t);
    p->d.end = (u_char *) p + size;
    p->d.next = NULL;
    p->d.failed = 0;

    size = size - sizeof(ngx_pool_t);
    p->max = (size < NGX_MAX_ALLOC_FROM_POOL) ? size : NGX_MAX_ALLOC_FROM_POOL;

    p->current = p;
    p->chain = NULL;
    p->large = NULL;
    p->cleanup = NULL;
    p->log = log;

    return p;
}
```

2.内存申请

内存分配逻辑:

分配一块内存，如果分配的内存size小于内存池的pool->max的限制，则属于小内存块分配，走小内存块分配逻辑；否则走大内存分配逻辑。
小内存分配逻辑：循环读取pool->d上的内存块，是否有足够的空间容纳需要分配的size，如果可以容纳，则直接分配内存；否则内存池需要申请新的内存块，调用ngx_palloc_block。
大内存分配逻辑：当分配的内存size大于内存池的pool->max的限制，则会直接调用ngx_palloc_large方法申请一块独立的内存块，并且将内存块挂载到pool->large的链表上进行统一管理。

**ngx_palloc**

```c
void *
ngx_palloc(ngx_pool_t *pool, size_t size)
{
#if !(NGX_DEBUG_PALLOC)
    if (size <= pool->max) {
        return ngx_palloc_small(pool, size, 1);
    }
#endif

    return ngx_palloc_large(pool, size);
}
```

**ngx_pnalloc**

```c
void *
ngx_pnalloc(ngx_pool_t *pool, size_t size)
{
#if !(NGX_DEBUG_PALLOC)
    if (size <= pool->max) {
        return ngx_palloc_small(pool, size, 0);
    }
#endif

    return ngx_palloc_large(pool, size);
}
```

**ngx_palloc_small**

```
#define ngx_align_ptr(p, a) (u_char *) (((uintptr_t) (p) + ((uintptr_t) a - 1)) & ~((uintptr_t) a - 1))

static void *
ngx_palloc_block(ngx_pool_t *pool, size_t size)
{
    u_char      *m;
    size_t       psize;
    ngx_pool_t  *p, *new;
    // 计算内存池第一个内存块的大小
    psize = (size_t) (pool->d.end - (u_char *) pool);
    // 分配和第一个内存块同样大小的内存块
    m = ngx_memalign(NGX_POOL_ALIGNMENT, psize, pool->log);
    if (m == NULL) {
        return NULL;
    }

    new = (ngx_pool_t *) m;
    // 设置新内存块的end
    new->d.end = m + psize;
    new->d.next = NULL;
    new->d.failed = 0;

    m += sizeof(ngx_pool_data_t);
    m = ngx_align_ptr(m, NGX_ALIGNMENT); //对m指针按4字节对齐处理
    new->d.last = m + size;

    for (p = pool->current; p->d.next; p = p->d.next) {
        if (p->d.failed++ > 4) {
            pool->current = p->d.next;
        }
    }

    p->d.next = new;

    return m;
}


static ngx_inline void *
ngx_palloc_small(ngx_pool_t *pool, size_t size, ngx_uint_t align)
{
    u_char      *m;
    ngx_pool_t  *p;

    p = pool->current;

    do {
        m = p->d.last;

        if (align) {
            m = ngx_align_ptr(m, NGX_ALIGNMENT);
        }

        if ((size_t) (p->d.end - m) >= size) {
            p->d.last = m + size;

            return m;
        }

        p = p->d.next;

    } while (p);

    return ngx_palloc_block(pool, size);
}
```

**ngx_palloc_large**

```c
static void *
ngx_palloc_large(ngx_pool_t *pool, size_t size)
{
    void              *p;
    ngx_uint_t         n;
    ngx_pool_large_t  *large;

    p = ngx_alloc(size, pool->log);
    if (p == NULL) {
        return NULL;
    }

    n = 0;

    for (large = pool->large; large; large = large->next) {
        if (large->alloc == NULL) {
            large->alloc = p;
            return p;
        }

        if (n++ > 3) {
            break;
        }
    }

    large = ngx_palloc_small(pool, sizeof(ngx_pool_large_t), 1);
    if (large == NULL) {
        ngx_free(p);
        return NULL;
    }

    large->alloc = p;
    large->next = pool->large;
    pool->large = large;

    return p;
}
```

3.内存池重置

释放大块内存，重置小块内存

```
void
ngx_reset_pool(ngx_pool_t *pool)
{
    ngx_pool_t        *p;
    ngx_pool_large_t  *l;

    for (l = pool->large; l; l = l->next) {
        if (l->alloc) {
            ngx_free(l->alloc);
        }
    }

    for (p = pool; p; p = p->d.next) {
        p->d.last = (u_char *) p + sizeof(ngx_pool_t);
        p->d.failed = 0;
    }

    pool->current = pool;
    pool->chain = NULL;
    pool->large = NULL;
}
```

4.内存池清理

独立大内存块的单独释放，可以使用 ngx_pfree

```
ngx_int_t
ngx_pfree(ngx_pool_t *pool, void *p)
{
    ngx_pool_large_t  *l;

    for (l = pool->large; l; l = l->next) {
        if (p == l->alloc) {
            ngx_log_debug1(NGX_LOG_DEBUG_ALLOC, pool->log, 0,
                           "free: %p", l->alloc);
            ngx_free(l->alloc);
            l->alloc = NULL;

            return NGX_OK;
        }
    }

    return NGX_DECLINED;
}
```

5.内存池销毁

```
void
ngx_destroy_pool(ngx_pool_t *pool)
{
    ngx_pool_t          *p, *n;
    ngx_pool_large_t    *l;
    ngx_pool_cleanup_t  *c;

    for (c = pool->cleanup; c; c = c->next) {
        if (c->handler) {
            ngx_log_debug1(NGX_LOG_DEBUG_ALLOC, pool->log, 0,
                           "run cleanup: %p", c);
            c->handler(c->data);
        }
    }

#if (NGX_DEBUG)

    /*
     * we could allocate the pool->log from this pool
     * so we cannot use this log while free()ing the pool
     */

    for (l = pool->large; l; l = l->next) {
        ngx_log_debug1(NGX_LOG_DEBUG_ALLOC, pool->log, 0, "free: %p", l->alloc);
    }

    for (p = pool, n = pool->d.next; /* void */; p = n, n = n->d.next) {
        ngx_log_debug2(NGX_LOG_DEBUG_ALLOC, pool->log, 0,
                       "free: %p, unused: %uz", p, p->d.end - p->d.last);

        if (n == NULL) {
            break;
        }
    }

#endif

    for (l = pool->large; l; l = l->next) {
        if (l->alloc) {
            ngx_free(l->alloc);
        }
    }

    for (p = pool, n = pool->d.next; /* void */; p = n, n = n->d.next) {
        ngx_free(p);

        if (n == NULL) {
            break;
        }
    }
}
```

