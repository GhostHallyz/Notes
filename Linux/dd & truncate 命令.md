## dd 命令

用于读取、转换并输出数据。

dd 可从标准输入或文件中读取数据，根据指定的格式来转换数据，再输出到文件、设备或标准输出。

**参数说明:**

- if=文件名：输入文件名，默认为标准输入。即指定源文件。
- of=文件名：输出文件名，默认为标准输出。即指定目的文件。
- ibs=bytes：一次读入bytes个字节，即指定一个块大小为bytes个字节。
  obs=bytes：一次输出bytes个字节，即指定一个块大小为bytes个字节。
  bs=bytes：同时设置读入/输出的块大小为bytes个字节。
- cbs=bytes：一次转换bytes个字节，即指定转换缓冲区大小。
- skip=blocks：从输入文件开头跳过blocks个块后再开始复制。
- seek=blocks：从输出文件开头跳过blocks个块后再开始复制。
- count=blocks：仅拷贝blocks个块，块大小等于ibs指定的字节数。
- conv=<关键字>，关键字可以有以下11种：
  - conversion：用指定的参数转换文件。
  - ascii：转换ebcdic为ascii
  - ebcdic：转换ascii为ebcdic
  - ibm：转换ascii为alternate ebcdic
  - block：把每一行转换为长度为cbs，不足部分用空格填充
  - unblock：使每一行的长度都为cbs，不足部分用空格填充
  - lcase：把大写字符转换为小写字符
  - ucase：把小写字符转换为大写字符
  - swap：交换输入的每对字节
  - noerror：出错时不停止
  - notrunc：不截短输出文件
  - sync：将每个输入块填充到ibs个字节，不足部分用空（NUL）字符补齐。
- --help：显示帮助信息
- --version：显示版本信息

```
# dd if=/dev/zero of=test1.dat bs=1M count=1
1+0 records in
1+0 records out
1048576 bytes (1.0 MB) copied, 0.0018056 s, 581 MB/s
```



## truncate 命令

```
长选项的强制性参数对于短选项也是强制性的。
  -c, --no-create 不创建任何文件
  -o, --io-blocks 将 SIZE 视为 IO 块数而不是字节数
  -r, --reference=RFILE 基于 RFILE 的基本大小
  -s, --size=SIZE 设置或调整文件大小 SIZE 字节
      --help 显示此帮助并退出
      --version 输出版本信息并退出

SIZE 是一个整数和可选单位（例如：10M 是 10\*1024\*1024）。单位
是 K、M、G、T、P、E、Z、Y（1024 的幂）或 KB、MB、...（1000 的幂）。
```

```
# truncate -s 10G 1.dat
# ls -l 1.dat
 -rw-r--r-- 1 root root 10737418240 Aug  3 23:14 1.dat
```

注意点：
这种文件被称为“空洞文件”，文件的部分内容并没有实际存在于硬盘上
du （disk use）：默认显示的是真正的磁盘占用。

## fallocate 命令

fallocate [选项] <文件名>

```
选项：
  -c, --collapse-range 从文件中删除一个范围
  -d, --dig-holes 检测零并替换为空洞
  -i, --insert-range 在范围内插入一个孔，移动现有数据
  -l, --length <num> 范围操作的长度，以字节为单位
  -n, --keep-size 保持文件的外观大小
  -o, --offset <num> 范围操作的偏移量，以字节为单位
  -p, --punch-hole 用一个洞替换一个范围（暗示 -n）
  -z, --zero-range 零并确保分配一个范围
  -x, --posix 使用 posix_fallocate(3) 而不是 fallocate(2)
  -v, --verbose 详细模式

  -h, --help 显示此帮助
  -V, --version 显示版本
```



```
#fallocate -l 10G test4
 fallocate: test4: fallocate failed: Operation not supported    //目录创建失败，只生成了空目录
# ls -l test4
 -rw-r--r-- 1 root root 0 Aug  4 00:29 test4
# du test4
 0  test4
```

