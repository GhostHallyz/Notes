## linux 通过pid 寻找程序路径的最简单命令(pwdx)

```
ps -ef| grep XXX

pwdx pid
```

# proc下查找

```bash
#1、先查进程 XXX
ps -ef| grep XXX
#2、每个进程启动之后在 /proc下面有一个于pid对应的路径，然后在进程文件中查找
ls -al /proc/pid
```

