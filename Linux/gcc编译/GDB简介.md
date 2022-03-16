# 1、GDB简介

  GDB（GNU Debugger）是一个由GNU开源组织发布的、UNIX/LINUX操作系统下的、基于命令行的、功能强大的程序调试工具。是linux系统环境下的C/C++开发者必须掌握的一个开发工具。

# 2、 GDB调试流程

## 2.1 准备

这里用c程序做演示；

```cpp
#include <stdio.h>

int main()
{
  printf("enter main()\n");
  int i = 0;
  for(; i<10; i++){
    printf("---%d\n",i);
  }
  printf("out main()\n");
  return 0;
}
```

  通过 gcc 的 `-g` 选项，将调试信息加到可执行文件中。

```
$ gcc -g hello.c -o hello
```

如果使用 Makefile 构建，一般要在 CFLAGS 中指定 `-g` 选项。

```
CFLAGS := -Wall -O2 -g
```

注意，给 GCC 编译器加上优化选项后，实际的执行顺序可能由于优化而与源代码顺序不同，因此利用调试器跟踪运行时，有时会执行到莫名奇妙的地方，从而造成混乱。

add：附加进程进入GDB调试

```bash
# 获取进程id
ps -ef | grep <process name>
# 通过进程id附加进程GDB调试
gdb -p <process id>
```

## 2.2 启动 GDB

使用命令"gdb <程序名>"启动 gdb

```
gdb test 
gdb -q test //表示不打印gdb版本信息，界面较为干净；

quit      退出gdb(quit 可简写为 q)
```

# 3、GDB基本调试命令

## 3.1 设置断点

3.1.1 break 设置断点

| 格式                  | 说明                                             |
| --------------------- | ------------------------------------------------ |
| break <函数名>        | 对当前正在执行的文件中的指定函数设置断点         |
| break <行号>          | 对当前正在执行的文件中的特定行设置断点           |
| break <文件名:行号>   | 对指定文件的指定行设置断点，最常用的设置断点方式 |
| break <文件名:函数名> | 对指定文件的指定函数设置断点                     |
| break <+/-偏移量>     | 当前指令行+/-偏移量处设置断点                    |
| break <*地址>         | 指定地址处设置断点                               |

```bash
b <文件名：n>    在指定文件的第n行处设置断点

b <函数名>       在函数的入口处设置断点
```

3.1.2 条件断点

```
b <函数名> if a＞b   条件断点设置
```

condition 命令的功能是：既可以为现有的普通断点、观察断点以及捕捉断点添加条件表达式，也可以对条件断点的条件表达式进行修改。

condition 命令没有缩写形式，使用方法很简单，语法格式如下：

```
(gdb) condition bnum expression  
(gdb) condition bnum
```

参数 bnum 用于代指目标断点的编号；参数 expression 表示为断点添加或修改的条件表达式。

以上 2 种语法格式中，第 1 种用于为 bnum 编号的断点添加或修改 expression 条件表达式；第 2 种用于删除 bnum 编号断点的条件表达式，使其变成普通的无条件断点。

ignore 命令也可以使一个断点成为条件断点，但这里的“条件”并非自定义的表达式，而仅为一个整数，它用来表示该断点失效的次数。也就会说，ignore 命令可以使目标断点暂时失去作用，当断点失效的次数超过指定次数时，断点的功能会自动恢复。

ignore 命令也没有缩写形式，其语法格式如下：

ignore bnum count

参数 bnum 为某个断点的编号；参数 count 用于指定该断点失效的次数。

3.1.3 删除断点

```
delete n                  删除第n个断点（delete 可简写为 d）
delete break/breakpoints  清除所有断点

clear 命令删除已定义的断点。可用命令包括：
   clear <函数名>
   clear <行号>
   clear <文件名:行号>
   clear <文件名:函数名>
```

3.1.4 启动或关闭断点

```
enable 断点号n       开启第n个断点
disable 断点号n      暂停第n个断点
```

3.1.5 查看断点

```
info b （info breakpoints/break）显示当前程序的断点设置情况 （info 可简写为 i）
```

3.1.6 其他断点

**临时断点（tbreak）**

tbreak 和 break 命令的用法和功能都非常相似，唯一的不同在于，使用 tbreak 命令打的断点仅会作用 1 次，即使程序暂停之后，该断点就会自动消失。

 **rbreak 命令**

和 break 和 tbreak 命令不同，rbreak 命令的作用对象是 C、C++ 程序中的函数，它会在指定函数的开头位置打断点。

tbreak 命令的使用语法格式为：

```
(gdb) tbreak regex
```

其中 regex 为一个正则表达式，程序中函数的函数名只要满足 regex 条件，tbreak 命令就会其内部的开头位置打断点。值得一提的是，tbreak 命令打的断点和 break 命令打断点的效果是一样的，会一直存在，不会自动消失。

**硬件断点（hbreak）**

适用于 ROM 空间等无法修改的内存区域中的程序，在有些架构中无法使用。

**临时硬件断点（thbreak）**

在运行到该处时暂停，此时断点会被删除，在只需要停止一次时用起来方便。

## 3.2设置监视点

  大型软件或大量使用指针的程序中，很难弄清变量在什么地方被改变，要想找到变量在何处被改变，可以使用 `watch` 命令（监视点，watchpoint）。格式如下：

- watch <表达式>。表达式发生变化时暂停运行。
- awatch <表达式>。表达式被访问、改变时暂停运行。
- rwatch <表达式>。表达式被访问时暂停运行。

  需要注意，设置监视点可能会降低运行速度。

## 3.2 运行

3.2.1 运行程序

3.2.2 继续运行

3.2.3 单步执行

  如果要逐条执行汇编指令，可以分别使用 nexti 和 stepi 命令。

3.2.4 运行到当前函数结束

3.2.5 运行到指定位置





```bash
run 运行程序，当遇到断点后，程序会在断点处停止运行，等待用户输入下一步的命令。(run 可简写为 r)

continue 继续执行，到下一个断点处（或运行结束）(continue 可简写为 c)

next 单步跟踪程序，当遇到函数调用时，也不进入此函数体(next 可简写为 n)
step 单步调试如果有函数调用，则进入函数(step 可简写为 s)

until      在一个循环体内单步跟踪时，这个命令可以运行程序直到退出循环体。
until n    运行至第n行，不仅仅用来跳出循环(可简写为 u)

finish     运行程序，直到当前函数完成返回，并打印函数返回时的堆栈地址和返回值及参数值等信息。

call 函数(参数)：调用程序中可见的函数，并传递“参数”，如：call gdb_test(55)


```

## 2.5 显示栈帧

  `backtrace` 命令可以在遇到断点或异常而暂停执行时显示栈帧，该命令简写为 `bt`。此外，backtrace 的别名还有 `where` 和 `info stack`。
  显示栈帧之后，就可以看出程序在何处停止，以及程序的调用路径。

## 2.6 显示变量

  `print` 命令可以显示变量，可以简写为 `p`。
  格式：`print 变量`。

## 2.7 显示寄存器

  `info registers` 可以显示寄存器，简写为 `info reg`。
  在寄存器名之前添加 $，显示寄存器的内容，例如 `p $eax`。
  `p/格式 $寄存器` 可以指定寄存器的显示格式，例如 `p/c $eax`。可使用的格式如下：

| 格式 | 说明                                        |
| ---- | ------------------------------------------- |
| x    | 显示为十六进制数                            |
| d    | 显示为十进制数                              |
| u    | 显示为无符号十进制数                        |
| o    | 显示为八进制数                              |
| t    | 显示为二进制数                              |
| a    | 地址                                        |
| c    | 显示为字符（ASCII）                         |
| f    | 浮点小数                                    |
| s    | 显示为字符串                                |
| i    | 显示为机器语言，仅在显示内存的 x 命令中可用 |

  程序指针可以写为 $pc，也可以写为 $eip，使用 `p $pc` 显示程序指针内容。程序指针指向当前程序的运行点的地址。
  `x` 命令可以显示内存的内容，格式：`x/<格式> <地址>`。例如 x/i $ps，显示汇编指令。
  一般使用 x 命令时，格式为 x/<NFU> <ADDR>。此处 ADDR 为希望显示的地址，N 为重复次数，F 为前面的显示格式，U 代表的单位如下：

| 单位 | 说明                   |
| ---- | ---------------------- |
| b    | 字节                   |
| h    | 半字（2 字节）         |
| w    | 字（4 字节）（默认值） |
| g    | 双字（8 字节）         |


  例如命令 x\10i $pc 显示从 pc 所指地址开始的 10 条指令。
  
  反汇编命令 `disassemble`，简写为 `disas`。格式：

- disassemble。反汇编当前整个函数。
- disassemble 程序计数器。反汇编程序计数器所在函数的整个函数。
- disassemble 开始地址 结束地址。反汇编从开始地址到结束地址之间的部分。







## 2.13 改变变量的值

  格式：`set variable <变量=表达式>`。例如命令 set variable options = 0，将变量 options 的值改成了 0。

## .3 查看源码

```
list  其作用就是列出程序的源代码，默认每次显示10行。(list 可简写为 l)

list ：不带参数，将接着上一次 list 命令的，输出下边的内容。
list <行号>：将显示当前文件以“行号”为中心的前后10行代码，如：list 1
list <函数名>：将显示“函数名”所在函数的源代码，如：list main
```

## 3.4 打印变量和表达式

```cpp
print <表达式>  其中“表达式”可以是任何当前正在被测试程序的有效表达式，比如当前正在调试C语言的程序，那么“表达式”可以是任何C语言的有效表达式，包括数字，变量甚至是函数调用。(print 可简写为 p)

print a：将显示整数 a 的值
print ++a：将把 a 中的值加1,并显示出来
print name：将显示字符串 name 的值
print gdb_test(22)：将以整数22作为参数调用 gdb_test() 函数
print gdb_test(a)：将以变量 a 作为参数调用 gdb_test() 函数


display <表达式>  在单步运行时将非常有用，使用display命令设置一个表达式后，它将在每次单步进行指令后，紧接着输出被设置的表达式及值。如： display a

watch <表达式>  设置一个监视点，一旦被监视的“表达式”的值改变，gdb将强行终止正在被调试的程序。如： watch a

whatis <变量或函数名>：查询变量类型或函数类型

info function <函数名>： 查询函数

info locals  显示当前堆栈页的所有变量
```

## 3.5 查看运行堆栈

| 格式                            | 说明                                 |
| ------------------------------- | ------------------------------------ |
| bt                              | 显示所有栈帧                         |
| bt <N>                          | 只显示开头 N 个栈帧                  |
| bt <-N>                         | 只显示最后 N 个栈帧                  |
| bt full 			bt full <N> | 不仅显示 backtrace，还要显示局部变量 |



```bash
where        当前运行的堆栈列表
backtrace    显示当前调用堆栈(可简写为 bt)

up/down      改变堆栈显示的深度

set args     参数:指定运行时的参数（-c 配置文件）
show args    查看设置好的参数

info program 查看程序的是否在运行，进程号，被暂停的原因。
```

## 2.14 生成内核转储文件

  使用 `generate-core-file` 可将调试中的进程生成内核转储文件。通过内核转储文件和调试对象，查看生成转储文件时的运行历史。
  `gcore` 命令可以从命令行直接生成内核转储文件。在命令行使用 `gcore pid`，其中 pid 为进程号。该命令无须停止正在运行的程序以获得内核转储文件，当需要在其他机器上单独分析问题原因，或是分析客户现场发生的问题时十分有用。

## 3.6 分割窗口

```bash
layout src   显示源代码窗口
layout asm   显示反汇编窗口
layout regs  显示源代码/反汇编和CPU寄存器窗口
layout next  切换到下一个布局模式
layout prev  切换到上一个布局模式
layout split 显示源代码和反汇编窗口

focus cmd/src/asm/regs/next/prev 切换当前窗口（focus 可简写为 fs）
winheight src/asm/regs +/- line 调整name窗口的高度
info win    显示窗口的大小
refresh     刷新所有窗口

tui reg next   显示下一组寄存器
tui reg system 显示系统寄存器

update       更新源代码窗口和当前执行点
tabset nchar 设置tab为nchar个字符

Ctrl + L    刷新窗口
Ctrl + x + 1（先按ctrl+x，再按1）  单窗口模式，显示一个窗口
Ctrl + x + 2（先按ctrl+x，再按2）  双窗口模式，显示两个窗口
Ctrl + x + a（先按ctrl+x，再按a）  回到传统模式，即退出layout
```

## 函数的参数地址显示

set print address
- set print address on
        打开地址输出，当程序显示函数信息时，GDB会显出函数的参数地址。系统默认为打开的，如：

        (gdb) f
        #0  set_quotes (lq=0x34c78 ">")
            at input.c:530
        530         if (lquote != def_lquote)
    
- set print address off
        关闭函数的参数地址显示，如：

        (gdb) set print addr off
        (gdb) f
        #0  set_quotes (lq=">") at input.c:530
        530         if (lquote != def_lquote)
    
- show print address
        查看当前地址显示选项是否打开。

## 结构体显示

set print pretty on
如果打开printf pretty这个选项，那么当GDB显示结构体时会比较漂亮。如：
            $1 = {
              next = 0x0,
              flags = {
                sweet = 1,
                sour = 1
              },
              meat = 0x54 "Pork"
            }
    set print pretty off
        关闭printf pretty这个选项，GDB显示结构体时会如下显示：
            $1 = {next = 0x0, flags = {sweet = 1, sour = 1}, meat = 0x54"Pork"}
    show print pretty
        查看GDB是如何显示结构体的。

set print sevenbit-strings
设置字符显示，是否按“/nnn”的格式显示，如果打开，则字符串或字符数据按/nnn显示，如“/065”。
    show print sevenbit-strings
        查看字符显示开关是否打开。

## 数组显示

set print array
- set print array on
        打开数组显示，打开后当数组显示时，每个元素占一行，如果不打开的话，每个元素则以逗号分隔。这个选项默认是关闭的。与之相关的两个命令如下，我就不再多说了。
    - set print array off
    - show print array

set print elements
这个选项主要是设置数组的，如果你的数组太大了，那么就可以指定一个来指定数据显示的最大长度，当到达这个长度时，GDB就不再往下显示了。如果设置为0，则表示不限制。
    show print elements
        查看print elements的选项信息。

set print null-stop
如果打开了这个选项，那么当显示字符串时，遇到结束符则停止显示。这个选项默认为off。

## 显示联合体

set print union

当此选项打开时，设置显示结构体时，显式其内的联合体数据。

例如有以下数据结构：
        typedef enum {Tree, Bug} Species;
        typedef enum {Big_tree, Acorn, Seedling} Tree_forms;
        typedef enum {Caterpillar, Cocoon, Butterfly}
                      Bug_forms;
        struct thing {
          Species it;
          union {
            Tree_forms tree;
            Bug_forms bug;
          } form;
        };
        struct thing foo = {Tree, {Acorn}};
        当打开这个开关时，执行 p foo 命令后，会如下显示：
            $1 = {it = Tree, form = {tree = Acorn, bug = Cocoon}}
        当关闭这个开关时，执行 p foo 命令后，会如下显示：
            $1 = {it = Tree, form = {...}}
    show print union
        查看联合体数据的显示方式

## 显示虚函数表

set print object
在C++中，如果一个对象指针指向其派生类，如果打开这个选项，GDB会自动按照虚方法调用的规则显示输出，如果关闭这个选项的话，GDB就不管虚函数表了。这个选项默认是off。
    show print object
        查看对象选项的设置。

## 显示静态数据成员

set print static-members
当此选项打开时，当显示一个C++对象中的内容时，显示其中的静态数据成员。默认是on。
    show print static-members
        查看静态数据成员选项设置。

## 显示虚函数表

set print vtbl
当此选项打开时，GDB将用比较规整的格式来显示虚函数表时。其默认是off。

```
set print vtbl on
set print vtbl off
show print vtbl      查看选项设置
```

## 打印数组元素的下标

set print array-indexes   

当此选项打开时，打印数组元素的下标。默认是 off 。

```
set print array-indexes on
set print array-indexes off
show print array-indexes       查看选项设置
```



### 4、GDB调试技巧

## 4.1 attach 到进程

  要调试已经启动的进程，或是调试陷入死循环而无法返回控制台的进程时，可以使用 `attach` 命令。格式：`attach <pid>`，执行这一命令可以 attach 到进程 ID 为 pid 的进程上。
  attach 之后就能使用普通的 gdb 命令。
  
  gdb 和进程分离时使用 `detach` 命令，调试的进程就从 gdb 的控制下释放出来。进程被 detach 后继续运行。
  
  进程信息可以用 `info proc` 命令显示。
  守护者进程在启动好子进程后，会自动关闭主进程，如果没有设定监控模式的话，gdb 会提示断开与进程的链接。所以必须设定监控对象，设置命令为 `set follow-fork-mode child/parent`。

## 4.2 条件断点

  `break <断点> if <条件>`，这条命令将测试给定的条件，如果为帧则暂停运行。
  
  如果断点已经存在，`condition <断点编号> <条件>` 命令给断点添加触发条件，`condition <断点编号>` 命令删除指定编号断点的触发条件。

## 4.3 反复执行

  `ignore <断点编号> <次数>`：在编号指定的断点、监视点或捕获点忽略指定的次数。
  `continue <次数>`： 达到指定次数前，执行到断电时不暂停。
  s/stepi/n/nexti <次数>：执行指定次数的相应命令。
  finish：执行完当前函数后暂停。
  until：执行完当前函数等代码块后暂停，如果是循环，则在执行完循环后暂停，常用于跳出循环。
  until <地址>：执行到指定地址停止。

## 4.4 断点命令

  `commands` 命令可以定义在断点终端后自动执行的命令。格式如下：

```
(gdb) commands <断点编号>
<命令>
...
end
```

## 4.5 值的历史

  通过 print 命令显示过的值会记录在内部的值历史中。这些值通过 $ 进行引用，使用 show value 命令可以显示历史中的最后 10 个值。

| 变量        | 说明                         |
| ----------- | ---------------------------- |
| $           | 值历史的最后一个值           |
| $n          | 值历史的第 n 个值            |
| $$          | 值历史的倒数第 2 个值        |
| $$n         | 值历史的倒数第 n 个值        |
| $_          | x 命令显示过的最后的地址     |
| $__         | x 命令显示过的最后的地址的值 |
| $_eexitcode | 调试过程中的程序的返回代码   |
| $bpnum      | 最后设置的断点编号           |

  还可以随意定义变量，变量以 $ 开头，由英文字母和数字组成。例如：

```
(gdb) set $i=0
(gdb) p $i
$i = 0
```

## 4.6 命令历史

  `show history` 将命令历史保存到文件中，默认命令历史文件位于 ./.gdb_history。
  set history expansion
  show history expansion

  可以使用 csh 风格的 ! 字符。

>   set history filename <文件名>
>   show history filename

  将命令历史保存到文件中。可以通过环境变量 GDBHISTFILE 改变默认文件名。

>   set history save
>   show history save

  启用命令历史保存到文件和恢复的功能。

>   set history size <数字>
>   show history size

  可设置保存到命令历史中的命令数量。默认值为 256。

# 5、内核转储

  内核转储（core dump）主要是用于在程序异常时，获取问题发生时的状态，定位代码异常的位置。

## 5.1 启用Linux 内核转储功能

  大多数 Linux 发行版默认关闭了内核转储功能，使用 `ulimit` 命令可以查看当前的内核转储功能是否有效。

```bash
$ ulimit -c       # -c 选项表示内核转储文件的大小限制
0                 # 0 表示内核转储无效

# 设置内核转储文件大小上限
$ ulimit -c unlimited   # unlimited 不限制内核转储文件的大小
or
$ ulimit -c 1073741824  # 设置上限1G，超过限制不产生内核转储文件
```



​    注意，若将产生的转储文件大小大于该数字时，将不会产生转储文件

上面所述的方法，只是在当前shell中生效,重启后丢失。永久生效的办法是在profile中添加：

```bash
$ vim /etc/profile
# 限制内核转储文件大小
ulimit -c 1073741824 
或
ulimit -c unlimited

source /etc/profile   # 使用source命令使配置立即生效

ulimit -a  # 查看设置结果
```



## 5.2 在专用目录中生成内核转储

缺省情况下，内核在coredump时所产生的core文件与该程序在相同的目录中，并且文件名固定为core。

这时，如果有多个程序产生core文件，或者同一个程序多次崩溃，就会重复覆盖同一个core文件。

可以通过修改kernel的参数，指定内核转储所生成的core文件的路径和文件名。在 /etc/sysctl.conf 中，设置 sysctl 变量 kernel.core_pattern 的值。

```bash
$ vim /etc/sysctl.conf
kernel.core_pattern = /var/coredump/core_%t_%e_%p #指定生成coredump文件的路径和文件名
kernel.core_uses_pid = 0  # 注意设置为1时，kernel.core_pattern 默认加上%p

$ sysctl –p /etc/sysctl.conf  # 使设置立即生效
```



kernel.core_pattern 中可以设置的格式符如下：

| 格式符 | 说明                                         |
| ------ | -------------------------------------------- |
| %%     | % 字符本身                                   |
| %p     | 被转储进程的进程 ID（PID）                   |
| %u     | 被转储进程的真实用户 ID（real UID）          |
| %g     | 被转储进程的真实组 ID（real GID）            |
| %s     | 引发转储的信号编号                           |
| %t     | 转储时刻（从 1970/1/1 0:00 开始的秒数）      |
| %h     | 主机名（同 uname(2) 返回的 nodename）        |
| %e     | 可执行文件名                                 |
| %c     | 转储文件的大小上限（内核版本 2.6.24 后可用） |

## 5.3 生成内核转储文件

运行异常的程序，程序崩溃产生coredump文件，这时可以使用gdb进行代码异常定位。

例如：

```cpp
#include <stdio.h>
int main(){
  int *p = NULL;
  *p = 0x1;
  return 0;
}
```



### 

### 使用GDB调试coredump文件

程序发生异常时会在当前目录下生成内核转储文件。例如程序 a.out 生成转储文件 core，使用以下方式启动 GDB，然后就可以使用GDB调试了：

```bash
# 进入工程目录
$ gcc -g dump.c -o dump
$ gdb -q -c /var/coredump/core_* dump
或
$ gdb -q dump /var/coredump/core_* 
```



 使用 GDB 的 list 命令可以查看附近的源代码。命令使用方法

| 格式                 | 说明                                  |
| -------------------- | ------------------------------------- |
| list <linenum>       | 显示程序第 linenum 行周围的源代码     |
| list <function>      | 显示函数名为 function 的函数的源代码  |
| list                 | 显示当前行后面的源代码                |
| list -               | 显示当前行前面的源代码                |
| set listsize <count> | 设置一次显示源代码的行数              |
| show listsize        | 查看当前 listsize 的设置              |
| list <first>,<last>  | 显示从 first 行到 last 行之间的源代码 |
| list ,<last>         | 显示从当前行到 last 行之间的源代码    |
| list +               | 往后显示源代码                        |

有些bug是不会导致程序crash的，比如死锁，这时程序已经不正常了，可是却没有coredump产生。如果环境又不允许gdb调试，就可以尝试强制该进程产生coredump文件。

一般情况下，可以利用 watchdog监控进程，当发现这些进程很长时间没有更新其heartbeat时，可以给这些进程发送可以导致其产生coredump的信号。根据 linux的信号默认的处理行为，SIGQUIT，SIGABRT, SIGFPE和SIGSEGV都可以让该进程产生coredump文件。这样我们可以通过coredump来得知死锁是否发生。 当然， 如果进程添加了这些信号的处理函数，那么就不会产生coredump了。

还有一种情况，进程并没有死锁或者block在某个位置，但是我们需要在某个指定位置进行调试，获取某些变量或者其它信息。但是，有可能是客户环境或者生产环境，不允许我们进行长时间的检测。那么，我们就需要通 过coredump来获得进程在运行到该点时的快照。 这个时候，可以利用gdb来手工产生coredump。在attach上这个进程时，在指定位置打上断点，当断点触发时，使用gdb的命令gcore，可以立即产生一个coredump。