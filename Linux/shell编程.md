```
使用变量
echo $your_name
echo ${#your_name}
readonly
unset

单引号
str='this is a string' + ' abc'
双引号
str="Hello, I know you are \"$your_name\"! \n"
echo -e $str

数组名=(值1 值2 ... 值n)
${数组名[下标]}
echo ${array_name[@]}
printf 格式化输出
printf format-string [arguments...]

注释
#
:<<EOF
...
EOF
注意：这边EOF可替换其他字符
```

Shell 基本运算符

**注意使用的是反引号** **`** **而不是单引号** **'**

实例

```
#!/bin/bash
val=`expr 2 + 2`
echo "两数之和为 : $val"
```

```
算术运算符运算符    说明                                       举例
+                加法                                       `expr $a + $b` 结果为 30。
-                减法                                       `expr $a - $b` 结果为 -10。
*                乘法                                       `expr $a \* $b` 结果为  200。
/                除法                                       `expr $b / $a` 结果为 2。
%                取余                                       `expr $b % $a` 结果为 0。
=                赋值                                        a=$b 将把变量 b 的值赋给 a。
==               相等。用于比较两个数字，相同则返回 true。        [ $a == $b ] 返回 false。
!=               不相等。 用于比较两个数字，不相同则返回 true。    [ $a != $b ] 返回 true。

关系运算符运算符    说明                                     举例
-eq      检测两个数是否相等，相等返回 true。                   [ $a -eq $b ] 返回 false。
-ne      检测两个数是否不相等，不相等返回 true。                [ $a -ne $b ] 返回 true。
-gt      检测左边的数是否大于右边的，如果是，则返回 true。        [ $a -gt $b ] 返回 false。
-lt      检测左边的数是否小于右边的，如果是，则返回 true。        [ $a -lt $b ] 返回 true。
-ge      检测左边的数是否大于等于右边的，如果是，则返回 true。     [ $a -ge $b ] 返回 false。
-le      检测左边的数是否小于等于右边的，如果是，则返回 true。     [ $a -le $b ] 返回 true。

布尔运算符
下表列出了常用的布尔运算符，假定变量 a 为 10，变量 b 为 20：
运算符    说明                                                                               举例
!             非运算，表达式为 true 则返回 false，否则返回 true。    [ ! false ] 返回 true。
-o          或运算，有一个表达式为 true 则返回 true。                    [ $a -lt 20 -o $b -gt 100 ] 返回 true。
-a          与运算，两个表达式都为 true 才返回 true。                    [ $a -lt 20 -a $b -gt 100 ] 返回 false。

逻辑运算符
以下介绍 Shell 的逻辑运算符，假定变量 a 为 10，变量 b 为 20:
运算符    说明                 举例
&&        逻辑的 AND    [[ $a -lt 100 && $b -gt 100 ]] 返回 false
||            逻辑的 OR       [[ $a -lt 100 || $b -gt 100 ]] 返回 true

字符串运算符
下表列出了常用的字符串运算符，假定变量 a 为 "abc"，变量 b 为 "efg"：
运算符    说明    举例
=    检测两个字符串是否相等，相等返回 true。    [ $a = $b ] 返回 false。
!=    检测两个字符串是否不相等，不相等返回 true。    [ $a != $b ] 返回 true。
-z    检测字符串长度是否为0，为0返回 true。    [ -z $a ] 返回 false。
-n    检测字符串长度是否不为 0，不为 0 返回 true。    [ -n "$a" ] 返回 true。
$    检测字符串是否为空，不为空返回 true。    [ $a ] 返回 true。

文件测试运算符
文件测试运算符用于检测 Unix 文件的各种属性。

属性检测描述如下：

操作符    说明    举例
-b file    检测文件是否是块设备文件，如果是，则返回 true。    [ -b $file ] 返回 false。
-c file    检测文件是否是字符设备文件，如果是，则返回 true。    [ -c $file ] 返回 false。
-d file    检测文件是否是目录，如果是，则返回 true。    [ -d $file ] 返回 false。
-f file    检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。    [ -f $file ] 返回 true。
-g file    检测文件是否设置了 SGID 位，如果是，则返回 true。    [ -g $file ] 返回 false。
-k file    检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。    [ -k $file ] 返回 false。
-p file    检测文件是否是有名管道，如果是，则返回 true。    [ -p $file ] 返回 false。
-u file    检测文件是否设置了 SUID 位，如果是，则返回 true。    [ -u $file ] 返回 false。
-r file    检测文件是否可读，如果是，则返回 true。    [ -r $file ] 返回 true。
-w file    检测文件是否可写，如果是，则返回 true。    [ -w $file ] 返回 true。
-x file    检测文件是否可执行，如果是，则返回 true。    [ -x $file ] 返回 true。
-s file    检测文件是否为空（文件大小是否大于0），不为空返回 true。    [ -s $file ] 返回 true。
-e file    检测文件（包括目录）是否存在，如果是，则返回 true。    [ -e $file ] 返回 true。
-S: 判断某文件是否 socket。
-L: 检测文件是否存在并且是一个符号链接。

test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。
数值测试
参数    说明
-eq    等于则为真
-ne    不等于则为真
-gt    大于则为真
-ge    大于等于则为真
-lt    小于则为真
-le    小于等于则为真
字符串测试
参数           说明
=                等于则为真
!=               不相等则为真
-z 字符串    字符串的长度为零则为真
-n 字符串    字符串的长度不为零则为真
文件测试
参数        说明
-e 文件名    如果文件存在则为真
-r 文件名    如果文件存在且可读则为真
-w 文件名    如果文件存在且可写则为真
-x 文件名    如果文件存在且可执行则为真
-s 文件名    如果文件存在且至少有一个字符则为真
-d 文件名    如果文件存在且为目录则为真
-f 文件名    如果文件存在且为普通文件则为真
-c 文件名    如果文件存在且为字符型特殊文件则为真
-b 文件名    如果文件存在且为块特殊文件则为真
```

**if-else if-else**

```
a=10
b=20
if [ $a == $b ]
then
   echo "a 等于 b"
elif [ $a -gt $b ]
then
   echo "a 大于 b"
elif [ $a -lt $b ]
then
   echo "a 小于 b"
else
   echo "没有符合的条件"
fi
```

for 循环

```
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

while 语句

```
#!/bin/bash
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

无限循环

```
while :
do
    command
done

或者
while true
do
    command
done

或者
for (( ; ; ))
```

until 循环

until 循环执行一系列命令直至条件为 true 时停止。

until 循环与 while 循环在处理方式上刚好相反。

一般 while 循环优于 until 循环，但在某些时候—也只是极少数情况下，until 循环更加有用。

```
#!/bin/bash
a=0
until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1`
done
```

case ... esaccase ... esac 为多选择语句，与其他语言中的 switch ... case 语句类似，是一种多分枝选择结构，每个 case 分支用右圆括号开始，用两个分号 ;; 表示 break，即执行结束，跳出整个 case ... esac 语句，esac（就是 case 反过来）作为结束标记。

可以用 case 语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。

```
echo '输入 1 到 4 之间的数字:
'echo '你输入的数字为:'
read aNum
case $aNum in    
    1)  echo '你选择了 1'    ;;    
    2)  echo '你选择了 2'    ;;    
    3)  echo '你选择了 3'    ;;    
    4)  echo '你选择了 4'    ;;    
    *)  echo '你没有输入 1 到 4 之间的数字'    ;;
esac
```

跳出循环break命令break命令允许跳出所有循环（终止执行后面的所有循环）。

continue它不会跳出所有循环，仅仅跳出当前循环。