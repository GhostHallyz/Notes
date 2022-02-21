## Linux下查看coredump文件是哪个程序生成的



**方法1**

\# strings core|grep _=

for example

\# strings core.5991 | grep _=

\# _=./a.out

这个coredump是由./a.out引起的

**方法2**

vim 打开core文件，用二进制方式显示，vim命令行中输入 :%!xxd，

在80，81行(不同程序不一样)看到程序名，

for example

80 00004f0: 612e 6f75 7400 0000 0000 0000 0000 0000  a.out...........

81 0000500: 2e2f 612e 6f75 7420 0000 0000 0000 0000  ./a.out ........ 

这个coredump是由./a.out引起的



解决 gdb 时的信号中断

```
Program received signal SIGUSR1, User defined signal 1.
```



```
gdb
> info signal SIGUSR1

>handle SIGUSR1 noprint nostop
```

