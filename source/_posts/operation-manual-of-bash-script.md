---
title: Bash脚本
date: 2018-02-06 11:17:32
tags: Manual
---

- 以 `#!/bin/bash`为首行

## 参数

### 外部传参

test.sh 
```
#!/bin/bash
cp $1 $2
```

$1 为第一个参数, $2 为第二个
用法: `bash test.sh test1.sh`
则$1被赋值为test.sh, $2被赋值为test1.sh

其他参数:

- $0 - 脚本名
zerotest.sh
```
#!/bin/bash
echo $0
```
运行`bash zerotest.sh`

结果: 
```
zerotest.sh
```

- $1 - $9 - 同上所述
- $# - 传入的参数个数
- $@ - 传入的所有参数
- $? - The exit status of the most recently run process.
- $$ - The process ID of the current script.
- $USER - The username of the user running the script.
- $HOSTNAME - The hostname of the machine the script is running on.
- $SECONDS - The number of seconds since the script was started.
- $RANDOM - Returns a different random number each time is it referred to.
- $LINENO - Returns the current line number in the Bash script.

### 内部定义

In:
```
var=Hello
varone='Hello World'
vartwo="More $myvar"
varthree='More $myvar'
varfour=Hello World

echo $var
echo $varone
echo $vartwo
echo $varthree
```
Out:
```
Hello
Hello World
More Hello World
More $myvar
# 会报错, 因为包含空格
-bash: World: command not found
```

- 还可以用一个执行块的返回结果作为参数的值
In:
```
myvar=$( ls /etc | wc -l )
echo the value of myvar: $myvar
```
Out:
```
the value of myvar: 87
```

### 不同脚本间传参

In:
script0.sh
```
#!/bin/bash
var1=blah
var2=foo
echo $0 : var1 -> $var1, var2 -> $var2
export var1
./script1.sh
echo $0 : var1 -> $var1, var2 -> $var2
```
script1.sh
```
#!/bin/bash
echo $0 :: var1 : $var1, var2 : $var2
var1=flop
var2=bleh
```

Out:
```
script0.sh :: var1 : blah, var2 : foo
script1.sh :: var1 : blah, var2 :
script0.sh :: var1 : blah, var2 : foo
```

### 运行中输入参数
```
read name 
read name age birthday # 可以一行内读取多个参数
read -p 'Username:' uservar # 带提示语
read -sp 'Password:' passvar # 密码(输入时不显示任何字符)
```

## 计算相关

### let

```
#!/bin/bash
let a=5+4
echo $a # 9
let "a = 5 + 4"
echo $a # 9
let a++
echo $a # 10
let "a = 4 * 5"
echo $a # 20
let "a = $1 + 30"
echo $a # 30 + first command line argument
```

### expr
逻辑上差不多可以等同于 echo let, 注意格式
```
#!/bin/bash
expr 5 + 4 # 9
expr "5 + 4" # "5 + 4"
expr 5+4 # 5+4
expr 5 \* $1 # 必须用\*表示乘
expr 11 % 2 # mod
a=$( expr 10 - 3 )
echo $a # 7
```

### $((计算语句))

```
a=$((3+5)) # 无格式要求
echo $a # 8
```

### ${井号var}
输出var的长度(换成'#'markdown居然无法解析....)

## 语法

通过缩进确定码块作用域
### if
```
if [ <some test> ]
then
	<commands>
	# 可用break/continue
elif [ <some test> ] 
then
	<different commands>
else
	<other commands>
fi
```

### switch
```
case <variable> in
	<pattern 1>)
		<commands>
		;;
	<pattern 2>)
		<other commands>
		;;
	*)
		;; # default
esac
```

### while
```
while [ <some test> ]
do
	<commands>
done
```

### until
```
until [ <some test> ]
do
	<commands>
done
```

while 和 until 的区别类似Java中的 while do 和 do while

### for
```
for var in <list>
do
	<commands>
done
```
遍历文件夹下的所有文件: `for i in $path/*; do`

### range

In:
```
echo {1..5}
echo {10..0..2} # 隔2输出
```

Out:
```
1 2 3 4 5
10 8 6 4 2 0
```

### select
In:
```
select_example.sh
#!/bin/bash
names='Kyle Cartman Stan Quit'
PS3='Select character: '
select name in $names
	do
		if [ $name == 'Quit' ]
		then
			break
		fi
	echo Hello $name
done

echo Bye
```

```
1) Kyle     3) Stan
2) Cartman  4) Quit
Select character: 2
Hello Cartman
Select Character: 1
Hello Kyle
Select character: 4
Bye
```
### 常用前缀/比较符
Operator|Description
--- | --- 
! EXPRESSION|The EXPRESSION is false.
-n STRING|The length of STRING is greater than zero.
-z STRING|The lengh of STRING is zero (ie it is empty).
STRING1 = STRING2|STRING1 is equal to STRING2
STRING1 != STRING2|STRING1 is not equal to STRING2
INTEGER1 -eq INTEGER2|INTEGER1 is numerically equal to INTEGER2
INTEGER1 -gt INTEGER2|INTEGER1 is numerically greater than INTEGER2
INTEGER1 -lt INTEGER2|INTEGER1 is numerically less than INTEGER2
-d FILE |FILE exists and is a directory.
-e FILE|FILE exists.
-r FILE|FILE exists and the read permission is granted.
-s FILE|FILE exists and it's size is greater than zero (ie. it is not empty).
-w FILE|FILE exists and the write permission is granted.
-x FILE|FILE exists and the execute permission is granted.

note: 001 = 1 -> false; 001 -eq 1 -> ture

### 函数

- 函数必须定义在调用之前
- 两种定义方式:
```
function_name () {
	<commands>
}

function function_name{
	<commands>
}
```

- 如果需要传递参数, 直接:
In:
```
funcion_arvg{
	echo $1
}

function_arvg Hello
```
Out:
```
Hello
```

- 如果需要有返回值:
In:
```
function_return{
	return 5
}

function_return
echo The funcion\'s return value is $?
```

Out:
```
The funcion's return value is 5
```
函数未出现错误时默认返回值为0

### 作用域
In:
```
#!/bin/bash
# Experimenting with variable scope
var_change () {
	local var1='local 1'
	echo Inside function: var1 is $var1 : var2 is $var2
	var1='changed again'
	var2='2 changed again'
}

var1='global 1'
var2='global 2'

echo Before function call: var1 is $var1 : var2 is $var2
var_change
echo After function call: var1 is $var1 : var2 is $var2
```

Out:
```
Before function call: var1 is global 1 : var2 is global 2
Inside function: var1 is local 1 : var2 is global 2
After function call: var1 is global 1 : var2 is 2 changed again
```
