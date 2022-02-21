**go运行时状态信息**

内存

runtime.ReadMemStats(&ms) 获取内存分配信息，可以定时收集，发送给时序数据库做监控统计

gc 数据

ReadGCStats 获取最近的GC统计信息，可以定时收集，发送给时序数据库做监控统计

协程数

runtime.NumGoroutine 获取当前协程数，可以定时收集，发送给时序数据库做监控统计

panic 堆栈信息

日志查询



pporf

实现

```

    flag.StringVar(&cpuprofile, "cpuprofile", "", "Write cpu profile to this file")
    flag.StringVar(&memprofile, "memprofile", "", "Write mem profile to this file")

    if cpuprofile != "" {
        f, err := os.Create(cpuprofile)
        if err != nil {
            fmt.Fprintf(os.Stderr, "Could not create cpuprofile file: %s\n", err)
            return 1
        }
        if err := pprof.StartCPUProfile(f); err != nil {
            panic(err)
        }
        defer pprof.StopCPUProfile()
        f.Close()
    }
    if memprofile != "" {
        f1, err := os.Create(cpuprofile)
        if err != nil {
            fmt.Fprintf(os.Stderr, "Could not create cpuprofile file: %s\n", err)
            return 1
        }
        runtime.GC() // get up-to-date statistics
        if pprof.WriteHeapProfile(f1); err != nil {
            panic(err)
        }
        f1.Close()
    }
```



```
一些可访问的/debug/pprof/目录下的路由：
cpu（CPU Profiling）: `$HOST/debug/pprof/profile`，默认进行 30s 的 CPU Profiling，得到一个分析用的 profile 文件

block（Block Profiling）：`$HOST/debug/pprof/block`，查看导致阻塞同步的堆栈跟踪

goroutine：`$HOST/debug/pprof/goroutine`，查看当前所有运行的 goroutines 堆栈跟踪

heap（Memory Profiling）: `$HOST/debug/pprof/heap`，查看活动对象的内存分配情况

mutex（Mutex Profiling）：`$HOST/debug/pprof/mutex`，查看导致互斥锁的竞争持有者的堆栈跟踪

threadcreate：`$HOST/debug/pprof/threadcreate`，查看创建新OS线程的堆栈跟踪

cmdline
allocs

ui交互
go tool pprof -http=:9000 http://localhost:7878/debug/pprof/profile?seconds=20
导出文件
go tool pprof http://localhost:7878/debug/pprof/profile?seconds=20 > cpu.out
go tool pprof -http=:9000 cpu.prof 或者 go-torch http://localhost:7878/debug/pprof/heap --colors mem  -f mem.svg

trace
go tool pprof http://localhost:7878/debug/pprof/trace?seconds=20 > trace.out
go tool trace -http='127.0.0.1:9001' trace.out

使用浏览器访问http://localhost:7878/debug/pprof/trace?seconds=20 下载trace日志文件
然后 go tool trace -http='127.0.0.1:9001' trace.out 分析
```



`pprof`也可以比较两个时间点的分配的内存的差值

```
导出时间点1的堆的profile: curl -s http://127.0.0.1:8080/debug/pprof/heap > base.heap
导出时间点2的堆的profile: curl -s http://127.0.0.1:8080/debug/pprof/heap > current.heap
比较这两个时间点的堆的差异: go tool pprof --base base.heap current.heap
```





gops

 (stack,gc,setgc,memstats,version,stats,trace,pprof-heap,pprof-cpu)



race （协程竞争检测，开发环境加上`-race`编译选项进行竞态检查，需要消耗更多cpu和内存资源，影响性能）

godebug 使用 gctrace （非侵入式查看 gc 信息，排查内存泄漏）

