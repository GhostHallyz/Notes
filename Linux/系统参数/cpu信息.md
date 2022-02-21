查看CPU信息

```
cat /proc/cpuinfo

processor　      ：系统中逻辑处理核的编号。对于单核处理器，则可认为是其CPU编号，对于多核处理器则可以是物理核或者使用超线程技术虚拟的逻辑核
vendor_id　      ：CPU制造商     
cpu family　     ：CPU产品系列代号
model            ：CPU属于其系列中的哪一代的代号
model name       ：CPU属于的名字及其编号、标称主频
stepping　       ：CPU属于制作更新版本
microcode        ：CPU 微代码
cpu MHz　        ：CPU的实际使用主频
cache size       ：CPU二级缓存大小
physical id      ：单个CPU的标号
siblings         ：单个CPU逻辑物理核数
core id          ：当前物理核在其所处CPU中的编号，这个编号不一定连续
cpu cores        ：该逻辑核所处CPU的物理核数
apicid           ：用来区分不同逻辑核的编号，系统中每个逻辑核的此编号必然不同，此编号不一定连续
initial apicid   ：初始的apicid数值
fpu              ：是否具有浮点运算单元（Floating Point Unit）
fpu_exception    ：是否支持浮点计算异常
cpuid level      ：执行cpuid指令前，eax寄存器中的值，根据不同的值cpuid指令会返回不同的内容
wp               ：表明当前CPU是否在内核态支持对用户空间的写保护（Write Protection）
flags            ：当前CPU支持的功能
bogomips         ：在系统内核启动时粗略测算的CPU速度（Million Instructions Per Second）
clflush size     ：每次刷新缓存的大小单位
cache_alignment  ：缓存地址对齐单位
address sizes    ：可访问地址空间位数
power management ：对能源管理的支持
```

查看cpu型号

```
cat /proc/cpuinfo | fgrep 'model name' | uniq -c
model name   : Intel(R) Xeon(R) Platinum 8255C CPU @ 2.50GHz
```

查看物理cpu个数

```
cat /proc/cpuinfo | fgrep 'physical id' | wc -l
```

查看核心个数

```
cat /proc/cpuinfo | fgrep 'core id' | wc -lcat /proc/cpuinfo| fgrep "cpu cores" | uniq
```

查看线程数

```
cat /proc/cpuinfo | fgrep 'processor' | wc -l
```

获取CPU最大频率

```
cat /proc/cpuinfo | fgrep 'cpu MHz' | sed 's/^.*: //g'
cat /proc/cpuinfo | fgrep 'cpu MHz' | awk -F ':' 'BEGIN{sum=0;num=0}{sum+=$2;num+=1}END{print sum/num}'
```

查看系统负载

```
uptime
14:59:17 up 9 days, 14 min, 3 users, load average: 0.00, 0.01, 0.05 
1. 当前时间 14:59:17 
2. 系统已运行的时间 9 days, 14 min 
3. 当前在线用户 3 user 4. 平均负载：0.00, 0.01, 0.05，最近1分钟、5分钟、15分钟系统的负载
```

最直接查看系统平均负载命令

```
cat /proc/loadavg
0.10 0.06 0.01 1/72 29632 
1.前3个数字表示平均负载：0.00, 0.01, 0.05，最近1分钟、5分钟、15分钟系统的负载 
2.分数，分母表示系统进程总数，分子表示正在运行的进程数 
3.最后一个数字表示最近运行的进程ID
```

查看CPU使用率

```
top
top -bn 1 | grep nginx | awk -F' ' 'BEGIN{mem=0;cpu=0}{mem+=$5;cpu+=$9}END{print mem,cpu}'
```


