##### 截取字符串:

```shell
${string: start :length}	从 string 字符串的左边第 start 个字符开始，向右截取 length 个字符。
${string: start}	从 string 字符串的左边第 start 个字符开始截取，直到最后。
${string: 0-start :length}	从 string 字符串的右边第 start 个字符开始，向右截取 length 个字符。
${string: 0-start}	从 string 字符串的右边第 start 个字符开始截取，直到最后。
${string#*chars}	从 string 字符串第一次出现 *chars 的位置开始，截取 *chars 右边的所有字符。
${string##*chars}	从 string 字符串最后一次出现 *chars 的位置开始，截取 *chars 右边的所有字符。
${string%*chars}	从 string 字符串第一次出现 *chars 的位置开始，截取 *chars 左边的所有字符。
${string%%*chars}	从 string 字符串最后一次出现 *chars 的位置开始，截取 *chars 左边的所有字符。
```

### 算数运算符

| +    |                     加法                      |    `expr $a + $b` 结果为 30。 |
| ---- | :-------------------------------------------: | ----------------------------: |
| -    |                     减法                      |   `expr $a - $b` 结果为 -10。 |
| *    |                     乘法                      | `expr $a \* $b` 结果为  200。 |
| /    |                     除法                      |     `expr $b / $a` 结果为 2。 |
| %    |                     取余                      |     `expr $b % $a` 结果为 0。 |
| =    |                     赋值                      |  a=$b 将把变量 b 的值赋给 a。 |
| ==   |   相等。用于比较两个数字，相同则返回 true。   |     [ $a == $b ] 返回 false。 |
| !=   | 不相等。用于比较两个数字，不相同则返回 true。 |      [ $a != $b ] 返回 true。 |

## 关系运算符

 下表列出了常用的关系运算符，假定变量 a 为 10，变量 b 为 20： 

| 运算符 |                         说明                          | 举例                       |
| :----- | :---------------------------------------------------: | :------------------------- |
| -eq    |          检测两个数是否相等，相等返回 true。          | [ $a -eq $b ] 返回 false。 |
| -ne    |        检测两个数是否不相等，不相等返回 true。        | [ $a -ne $b ] 返回 true。  |
| -gt    |   检测左边的数是否大于右边的，如果是，则返回 true。   | [ $a -gt $b ] 返回 false。 |
| -lt    |   检测左边的数是否小于右边的，如果是，则返回 true。   | [ $a -lt $b ] 返回 true。  |
| -ge    | 检测左边的数是否大于等于右边的，如果是，则返回 true。 | [ $a -ge $b ] 返回 false。 |
| -le    | 检测左边的数是否小于等于右边的，如果是，则返回 true。 | [ $a -le $b ] 返回 true。  |

## 布尔运算符

 下表列出了常用的布尔运算符，假定变量 a 为 10，变量 b 为 20： 

| 运算符 | 说明                                                | 举例                                     |
| :----- | :-------------------------------------------------- | :--------------------------------------- |
| !      | 非运算，表达式为 true 则返回 false，否则返回 true。 | [ ! false ] 返回 true。                  |
| -o     | 或运算，有一个表达式为 true 则返回 true。           | [ $a -lt 20 -o $b -gt 100 ] 返回 true。  |
| -a     | 与运算，两个表达式都为 true 才返回 true。           | [ $a -lt 20 -a $b -gt 100 ] 返回 false。 |

## 逻辑运算符

以下介绍 Shell 的逻辑运算符，假定变量 a 为 10，变量 b 为 20:

| 运算符 | 说明       | 举例                                       |
| :----- | :--------- | :----------------------------------------- |
| &&     | 逻辑的 AND | [[ $a -lt 100 && $b -gt 100 ]] 返回 false  |
| \|\|   | 逻辑的 OR  | [[ $a -lt 100 \|\| $b -gt 100 ]] 返回 true |

## 字符串运算符

下表列出了常用的字符串运算符，假定变量 a 为 "abc"，变量 b 为 "efg"：

| 运算符 | 说明                                      | 举例                     |
| :----- | :---------------------------------------- | :----------------------- |
| =      | 检测两个字符串是否相等，相等返回 true。   | [ $a = $b ] 返回 false。 |
| !=     | 检测两个字符串是否相等，不相等返回 true。 | [ $a != $b ] 返回 true。 |
| -z     | 检测字符串长度是否为0，为0返回 true。     | [ -z $a ] 返回 false。   |
| -n     | 检测字符串长度是否为0，不为0返回 true。   | [ -n "$a" ] 返回 true。  |
| $      | 检测字符串是否为空，不为空返回 true。     | [ $a ] 返回 true。       |

## 文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性。

属性检测描述如下：

| 操作符  | 说明                                                         | 举例                      |
| :------ | :----------------------------------------------------------- | :------------------------ |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -c $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] 返回 true。  |

其他检查符：

- **-S**: 判断某文件是否 socket。

- **-L**: 检测文件是否存在并且是一个符号链接。

### if 条件语句

简单的 if else-if else 的条件语句示例

```shell
#!/bin/bash
a=3;
b=9;
if [ $a -eq $b ]
then
   echo "a 等于 b"
elif [ $a -gt $b ]
then
   echo "a 大于 b"
else
   echo "a 小于 b"
fi
```

输出结果：

```shell
a 小于 b
```

### for 循环语句

**输出当前列表中的数据：**

```shell
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

**产生 10 个随机数：**

```shell
#!/bin/bash
for i in {0..9};
do 
   echo $RANDOM;
done
```

**输出1到5:**

通常情况下 shell 变量调用需要加 $,但是 for 的 (()) 中不需要,下面来看一个例子：

```shell
#!/bin/bash
for((i=1;i<=5;i++));do
    echo $i;
done;
```

### while 语句

**基本的 while 循环语句：**

```shell
#!/bin/bash
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

**while循环可用于读取键盘信息：**

```shell
echo '按下 <CTRL-D> 退出'
echo -n '输入你最喜欢的电影: '
while read FILM
do
    echo "是的！$FILM 是一个好电影"
done
```

输出内容:

```shell
按下 <CTRL-D> 退出
输入你最喜欢的电影: 变形金刚
是的！变形金刚 是一个好电影
```

**无限循环：**

```shell
while true
do
    command
done
```

## shell 函数

### 不带参数没有返回值的函数

```shell
#!/bin/bash
hello(){
    echo "这是我的第一个 shell 函数!"
}
echo "-----函数开始执行-----"
hello
echo "-----函数执行完毕-----"
```

输出结果：

```shell
-----函数开始执行-----
这是我的第一个 shell 函数!
-----函数执行完毕-----
```

### 有返回值的函数

**输入两个数字之后相加并返回结果：**

```shell
#!/bin/bash
funWithReturn(){
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $?"
```

输出结果：

```shell
输入第一个数字: 
1
输入第二个数字: 
2
两个数字分别为 1 和 2 !
输入的两个数字之和为 3
```

### 带参数的函数

```shell
#!/bin/bash
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

输出结果：

```shell
第一个参数为 1 !
第二个参数为 2 !
第十个参数为 10 !
第十个参数为 34 !
第十一个参数为 73 !
参数总数有 11 个!
作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !
```