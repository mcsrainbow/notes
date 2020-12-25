#### 1. 变量定义
```bash
## var无定义时取值hello
[dong@idc1-server1 ~]$ echo ${var}

[dong@idc1-server1 ~]$ echo ${var-hello}
hello
[dong@idc1-server1 ~]$ echo ${var:-hello}
hello

## var无定义时取值hello且赋值为hello
[dong@idc1-server1 ~]$ echo ${var=hello}
hello
[dong@idc1-server1 ~]$ echo ${var:=hello}
hello
[dong@idc1-server1 ~]$ echo ${var}
hello

## var有定义时取值$var
[dong@idc1-server1 ~]$ echo ${var-bye}
hello

## var有定义时取值bye
[dong@idc1-server1 ~]$ echo ${var+bye}
bye
[dong@idc1-server1 ~]$ echo ${var:+bye}
bye
```

#### 2. 内部变量
```bash
[dong@idc1-server1 ~]$ cat magic.sh
echo $#   #参数个数
echo $*   #所有参数
echo $@     #所有参数
echo ${@:2} #从第2个参数开始到最后的所有参数
echo $?   #上次执行命令返回值
echo $$   #当前shell的进程标识符
/bin/true &  #用&将一个命令放到后台执行
echo $!   #最后一个用&后台执行的命令的进程标识符
echo $0   #当前shell脚本的名称
echo $1   #第一个参数
args=$#   #将args赋值为参数个数
echo ${!#}  #最后一个参数
echo ${!args}  #最后一个参数


## 脚本执行结果
[dong@idc1-server1 ~]$ ./magic.sh param1 param2 param3
3
param1 param2 param3
param1 param2 param3
param2 param3
0
46490
46491
./magic.sh
param1
param3
param3
```

#### 3. 字符串截取
```bash
# 定义字符串
[dong@idc1-server1 ~]$ var=http://heylinux.com/archives/3668.html

# 显示字符串中的字符数量
[dong@idc1-server1 ~]$ echo ${#var}
38

# #号截取，删除左边字符，保留右边字符；
# 其中var是变量名，#号是运算符，*//表示从左边开始删除第一个//号及左边的所有字符；
# 即删除 http://
[dong@idc1-server1 ~]$ echo ${var#*//}
heylinux.com/archives/3668.html

# ##号截取，删除左边字符，保留右边字符；
# ##*/表示从左边开始删除最后(最右边)一个/号及左边的所有字符；
# 即删除 http://heylinux.com/archives/
[dong@idc1-server1 ~]$ echo ${var##*/}
3668.html

# %号截取，删除右边字符，保留左边字符；
# %/*表示从右边开始，删除第一个/号及右边的字符；
[dong@idc1-server1 ~]$ echo ${var%/*}
http://heylinux.com/archives

# %%号截取，删除右边字符，保留左边字符；
# %%/*表示从右边开始，删除最后(最左边)一个/号及右边的字符；
[dong@idc1-server1 ~]$ echo ${var%%/*}
http:

# 从左边第几个字符开始，及字符的个数；
# 其中的0表示左边第一个字符开始，5表示字符的总个数；
[dong@idc1-server1 ~]$ echo ${var:0:5}
http:

# 从左边第几个字符开始，一直到结束；
# 其中的7表示左边第八个字符开始，一直到结束；
[dong@idc1-server1 ~]$ echo ${var:7}
heylinux.com/archives/3668.html

# 从右边第几个字符开始，及字符的个数；
# 其中的0-7表示右边算起第七个字符开始，3表示字符的个数；
[dong@idc1-server1 ~]$ echo ${var:0-7:3}
68.

# 从右边第几个字符开始，一直到结束；
[dong@idc1-server1 ~]$ echo ${var:0-7}
68.html

# 重新定义字符串，方面下面的替换操作
[dong@idc1-server1 ~]$ var=http://heylinux.com/tag1/tag2/tag3/3668.html

# 将字符串中的第一个tag替换为page
[dong@idc1-server1 ~]$ echo ${var/tag/page}
http://heylinux.com/page1/tag2/tag3/3668.html

# 将字符串中的所有tag替换为page
[dong@idc1-server1 ~]$ echo ${var//tag/page}
http://heylinux.com/page1/page2/page3/3668.html

# 将字符串中的第一个tag替换为空字符串(即删除)
[dong@idc1-server1 ~]$ echo ${var/tag}
http://heylinux.com/1/tag2/tag3/3668.html

# 将字符串中的所有tag替换为空字符串(即删除)
[dong@idc1-server1 ~]$ echo ${var//tag}
http://heylinux.com/1/2/3/3668.html

# 将字符串中的前缀http替换为ftp
[dong@idc1-server1 ~]$ echo ${var/#http/ftp}
ftp://heylinux.com/tag1/tag2/tag3/3668.html

# 将字符串中的后缀html替换为php
[dong@idc1-server1 ~]$ echo ${var/%html/php}
http://heylinux.com/tag1/tag2/tag3/3668.php
```

#### 4. 字符串判断
```bash
[[ "${string}" =~ "*" ]] #判断字符串中是否含有符号'*'
[[ "${string}" =~ "rm" ]] #判断字符串中是否含有字符'rm'
[[ "${string}" != "-*" ]] #判断字符串是否由符号'-'开头
```

#### 5. 判断与运算
```bash
[dong@idc1-server1 ~]$ cat magic.sh
#!/bin/bash
echo $(date +%Y%m%d).gz #将命令执行结果作为变量值使用

echo $((4+2*(8-1*6)-4/2)) #加减乘除混合运算
echo $((7%3)) #7除以3取余数
echo $((${RANDOM}%100)) #取100以内的随机数

echo {1..10} #数字1到10的range
echo {01..10} #数字1到10的range，不足两位的用0补齐
echo {01..10..2} #数字1到10的range，公差为2
echo {a..f} #字母a到f的range
echo {a..f..2} #字母a到f的range，公差为2

[ $var != "no" ] && echo "cool.1" #脆弱的实现，当var无定义时会报告语法错误
[ $num -ne 5 ] && echo "yeah.1" #脆弱的实现，当num无定义时会报告语法错误

[ "$var" != "no" ] && echo "cool.2" #健壮的实现，当var无定义时仍能进行判断
[[ $var != "no" ]] && echo "cool.3" #健壮的实现，当var无定义时仍能进行判断
[[ "$var" != "no" ]] && echo "cool.4" #更健壮的实现，当var无定义时仍能进行判断
[[ $num -ne 5 ]] && echo "yeah.2" #健壮的实现，当num无定义时仍能进行判断

(($num>=5)) && echo "wow.1" #脆弱的C-style实现，当num无定义时会报告语法错误，可用的符号有<,>,<=,>=,!=

for i in {1..5}; do echo $i; done #for循环实现

for ((i=1; i<=5; i++)); do echo $i; done #C-style的for循环实现

# 数组的实现
array=(
array_value1
array_value2
array_value3
)
echo ${array[0]} #数组中第一个值
echo ${array[@]} #数组中所有值
echo ${array[*]} #数组中所有值
echo ${#array[*]} #数组中值个数
echo ${#array[@]} #数组中值个数

# 动态增加数组元素
array+=(arrary_new1)
array+=(arrary_new2)
array+=(arrary_new3)
echo ${array[*]}

# 字典的实现
declare -A dict=(
["dict_key1"]="dict_value1"
["dict_key2"]="dict_value2"
)
echo ${dict["dict_key1"]} #字典中第一个Value
echo ${dict[@]} #字典中所有Value
echo ${!dict[@]} #字典中所有Key

# 普通赋值操作
a=a+5
echo $a

# 算术型赋值操作
let b=b+5
echo $b

# 更健壮的算术型赋值操作
let "c += 5"
echo $c

# 命令生成器，避免变量与语法的混乱
maxnum=100
logfile=/var/log/httpd/access_log
awk '{print $1}' ${logfile} |sort |uniq -c |awk '($1 > ${maxnum}){print $2}'
# 在上面的命令中第二个awk的单引号会导致${maxnum}无法被解析为100，从而导致语法错误
# 正确的做法应该是先将命令生成好，然后再通过eval执行，如下所示：
command="awk '{print \$1}' ${logfile} |sort |uniq -c |awk '(\$1 > ${maxnum}){print \$2}'"
eval ${command}

# 通过变量PIPESTATUS获取管道命令每一层的返回值
lsof | grep access_log | grep -v httpd
return_codes=(${PIPESTATUS[*]})
echo ${return_codes[*]}

# 通过while命令循环按行获取多个参数
echo -e "A B C\n 1 2 3" | while read param1 param2 param3; do
  echo ${param1}
  echo ${param2} ${param3}
done


## 脚本执行结果
[dong@idc1-server1 ~]$ ./magic.sh
20150609.gz

6
1
56

1 2 3 4 5 6 7 8 9 10
01 02 03 04 05 06 07 08 09 10
01 03 05 07 09
a b c d e f
a c e

./magic.sh: line 8: [: !=: unary operator expected
./magic.sh: line 9: [: -ne: unary operator expected

cool.2
cool.3
cool.4
yeah.2

-bash: ((: >=5: syntax error: operand expected (error token is ">=5")

1
2
3
4
5

1
2
3
4
5

array_value1 array_value2 array_value3
array_value1 array_value2 array_value3
array_value1 array_value2 array_value3
3
3

array_value1 array_value2 array_value3 arrary_new1 arrary_new2 arrary_new3

dict_value1
dict_value1 dict_value2
dict_key1 dict_key2

a+5

5

5

awk: ($1 > ${maxnum}){print $2}
awk:        ^ syntax error
awk: ($1 > ${maxnum}){print $2}
awk:                 ^ syntax error

10.100.1.11

0 0 1

A
B C
1
2 3
```

#### 6. 编码规范
```
https://google.github.io/styleguide/shell.xml
```
