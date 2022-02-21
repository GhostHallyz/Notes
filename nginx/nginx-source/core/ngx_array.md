数组结构

```
//  数组 ngx_array 数据结构
typedef struct {
    void        *elts;   // 指向数组第一个元素指针
    ngx_uint_t   nelts;  // 未使用元素的索引
    size_t       size;   // 每个元素的大小，元素大小固定
    ngx_uint_t   nalloc; // 分配多少个元素
    ngx_pool_t  *pool;   // 内存池
} ngx_array_t;
```



| 函数                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| static ngx_inline ngx_int_t<br/>ngx_array_init(ngx_array_t *array, ngx_pool_t *pool, ngx_uint_t n, size_t size) | 初始化ngx_array_t类型的对象                                  |
| ngx_array_t * ngx_array_create(ngx_pool_t *p, ngx_uint_t n, size_t size) | 创建数组                                                     |
| void ngx_array_destroy(ngx_array_t *a)                       | 销毁数组                                                     |
| void * ngx_array_push(ngx_array_t *a)                        | 从ngx_array_t对象中再分配一个元素，如果ngx_array_t中没有可用的空闲空间则从ngx_pool_t中申请 |
| void * ngx_array_push_n(ngx_array_t *a, ngx_uint_t n)        | 从ngx_array_t对象中再分配n个元素，如果ngx_array_t中没有可用的空闲空间则从ngx_pool_t中申请 |

