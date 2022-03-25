# chmod

控制用户对文件的权限的命令

```
其中：

u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。
+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。

其他参数说明：
-c : 若该文件权限确实已经更改，才显示其更改动作
-f : 若该文件权限无法被更改也不要显示错误讯息
-v : 显示权限变更的详细资料
-R : 对目前目录下的所有文件与子目录进行相同的权限变更(即以递归的方式逐个变更)
--help : 显示辅助说明
--version : 显示版本
```

示例：

```bash
$ chmod a+r file	给file的所有用户增加读权限
$ chmod 777 file
```



# chown

用于设置文件所有者和文件关联组的命令

示例1：

```bash
[root@centos test]# ll -l
total 0
-rw-r--r-- 1 root root 0 Aug 24 17:36 a.txt
[root@centos test]# chown nobody a.txt
[root@centos test]# ll -l
total 0
-rw-r--r-- 1 nobody root 0 Aug 24 17:36 a.txt
[root@centos test]#
```

示例2：

```bash
[root@centos test]# ll -l
total 0
-rw-r--r-- 1 nobody root 0 Aug 24 17:36 a.txt
[root@centos test]# chown root:root a.txt
[root@centos test]# ll -l
total 0
-rw-r--r-- 1 root root 0 Aug 24 17:36 a.txt
[root@centos test]#
```



# chgrp

用于变更文件或目录的所属群组

```
参数说明
　　-c或--changes 效果类似"-v"参数，但仅回报更改的部分。

　　-f或--quiet或--silent 　不显示错误信息。

　　-h或--no-dereference 　只对符号连接的文件作修改，而不更动其他任何相关文件。

　　-R或--recursive 　递归处理，将指定目录下的所有文件及子目录一并处理。

　　-v或--verbose 　显示指令执行过程。

　　--help 　在线帮助。

　　--reference=<参考文件或目录> 　把指定文件或目录的所属群组全部设成和参考文件或目录的所属群组相同。

　　--version 　显示版本信息。
```

示例：

```bash
[root@centos test]# ll -l
total 0
-rw-r--r-- 1 root root 0 Aug 24 17:36 a.txt
[root@centos test]# chgrp nobody a.txt
[root@centos test]# ll -l
total 0
-rw-r--r-- 1 root nobody 0 Aug 24 17:36 a.txt
[root@centos test]#
```

