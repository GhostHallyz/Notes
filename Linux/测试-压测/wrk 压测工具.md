# wrk 压测工具

## 安装

```
git clone https://github.com/wg/wrk.git  
cd wrk  
make  
```

## 常用参数类型

```
Usage: wrk <options> <url>
  Options:
    -c, --connections <N>  Connections to keep open
    -d, --duration    <T>  Duration of test, e.g. 2s, 2m, 2h
    -t, --threads     <N>  Number of threads to use

    -s, --script      <S>  Load Lua script file
    -H, --header      <H>  Add header to request, e.g. "User-Agent: wrk"
        --latency          Print latency statistics（延迟统计）
        --timeout     <T>  Socket/request timeout
    -v, --version          Print version details

  Numeric arguments may include a SI unit (1k, 1M, 1G)
  Time arguments may include a time unit (2s, 2m, 2h)
```



## 高级用法

wrk可以结合lua来做,通过wrk提供的几个lua函数来对请求进行修改,结果输出、设置延迟等操作。下面来看看wrk提供的几个lua函数:

### **lua声明周期** 

共有三个阶段，启动阶段，运行阶段，结束阶段。wrk支持在这三个阶段对压测进行个性化。

#### 启动阶段

##### setup 函数

 这个函数在目标 IP 地址已经解析完, 并且所有 thread 已经生成, 但是还没有开始时被调用. 每个线程执行一次这个函数.
 可以通过thread:get(name),  thread:set(name, value)设置线程级别的变量.

#### 运行阶段

##### init 函数

 每次请求发送之前被调用.
 可以接受 wrk 命令行的额外参数. 通过 -- 指定.

setup方法中可操作该thread对象，获取信息、存储信息、甚至关闭该线程。

```
thread.addr - get or set the thread's server address
thread:get(name) - get the value of a global in the thread's env
thread:set(name, value) - set the value of a global in the thread's env
thread:stop() - stop the thread
```

##### delay函数

 这个函数返回一个数值, 在这次请求执行完以后延迟多长时间执行下一个请求. 可以对应 thinking time 的场景.

##### request函数

 通过这个函数可以每次请求之前修改本次请求的属性. 返回一个字符串. 这个函数要慎用, 会影响测试端性能.

```
request = function()
    wrk.method = "GET"
    wrk.headers["Host"] = "www.test.com"
    wrk.headers["x-header-trace"] = "all"
    return wrk.format("GET", wrk.path)
end
```

##### response函数

 每次请求返回以后被调用. 可以根据响应内容做特殊处理, 比如遇到特殊响应停止执行测试, 或输出到控制台等等.

```jsx
function response(status, headers, body)  
   if status ~= 200 then  
      print(body)  
      wrk.thread:stop()  
   end  
end  
```

#### 结束阶段

##### done函数

 在所有请求执行完以后调用, 一般用于自定义统计结果.

```php
done = function(summary, latency, requests)  
   io.write("------------------------------\n")  
   for _, p in pairs({ 50, 90, 99, 99.999 }) do  
      n = latency:percentile(p)  
      io.write(string.format("%g%%,%d\n", p, n))  
   end  
end  
```



#### wrk官网提供的setup.lua实例

```bash
-- example script that demonstrates use of setup() to pass
-- data to and from the threads

local counter = 1
local threads = {}

function setup(thread)
   thread:set("id", counter)
   table.insert(threads, thread)
   counter = counter + 1
end

function init(args)
   requests  = 0
   responses = 0

   local msg = "thread %d created"
   print(msg:format(id))
end

function request()
   requests = requests + 1
   return wrk.request()
end

function response(status, headers, body)
   responses = responses + 1
end

function done(summary, latency, requests)
   for index, thread in ipairs(threads) do
      local id        = thread:get("id")
      local requests  = thread:get("requests")
      local responses = thread:get("responses")
      local msg = "thread %d made %d requests and got %d responses"
      print(msg:format(id, requests, responses))
   end
end
```

使用setup.lua:

```bash
[root@jerrik wrk]# wrk -t 4 -c 100 -d 20s --latency -s scripts/setup.lua https://www.baidu.com
thread 1 created
thread 2 created
thread 3 created
thread 4 created
Running 20s test @ https://www.baidu.com
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   251.75ms  336.19ms   2.00s    86.89%
    Req/Sec   138.51     69.90   690.00     71.23%
  Latency Distribution
     50%  215.74ms
     75%  401.87ms
     90%  664.84ms
     99%    1.54s 
  11021 requests in 20.02s, 162.82MB read
  Socket errors: connect 0, read 3, write 0, timeout 50
Requests/sec:    550.62
Transfer/sec:      8.13MB
thread 1 made 2945 requests and got 2919 responses
thread 2 made 2831 requests and got 2807 responses
thread 3 made 2772 requests and got 2747 responses
thread 4 made 2573 requests and got 2548 responses
[root@jerrik wrk]# 
```

将每个线程的请求数和响应数输出来了。其它更多使用可以参考github script目录下的lua脚本。



#### 方法

wrk.fomat   wrk.lookup    wrk.connect         

```
function wrk.format(method, path, headers, body)  
--根据参数和全局变量wrk，生成一个HTTP rquest string。

function wrk.lookup(host, service)  
--给定host和service（port/well known service name），返回所有可用的服务器地址信息。

function wrk.connect(addr)  
--测试与给定的服务器地址信息是否可以成功创建连接
```