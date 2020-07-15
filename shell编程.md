

# Shell编程

2020/1/23	15:36双桥BEGIN



/etc/shells文件

​	当前系统所支持的shell

```
[root@localhost sjhvg]# cat /etc/shells 
/bin/sh
/bin/bash
/sbin/nologin
/bin/dash
/bin/tcsh
/bin/csh
```



#### echo

​	-e	支持反斜线控制的字符转换(也就是支持转义字符)

​	-n	输出不换行

```
[root@localhost sjhvg]# echo -n 123
123[root@localhost sjhvg]# 
```



```
[root@localhost sjhvg]# echo -e \a
a
[root@localhost sjhvg]# echo -e "\a"	输出警告音，好像必须加在双引号或者单引号里才会转义

```



#### Shell脚本

**#!/bin/bash**



#### 命令

##### 	history 

​		文件位置在~/.bash_history，默认在文件中只记录1000条，记录最大数量在/etc/profile里的HISTSIZE=1000

​		这个命令，当前登录用户所输入的命令不会立即同步到文件中，只有用户注销以后才会更新到文件中

​		-w	使当前缓冲区中的命令立即同步到文件中

​		-c	清空缓冲区的历史命令

| 操作  |                   说明                   |
| :---: | :--------------------------------------: |
|  !n   |    执行第n条历史命令(在缓冲区的第n条)    |
|  !!   |              执行上一条命令              |
| !字串 |    重复执行最后一条以该字串开头的命令    |
|  !$   | 重复上一条命令的最后一个参数(当命令执行) |

##### 	source

​		重新加载配置文件

​		也可以用.来代替

```
[root@localhost ~]# . /etc/profile
[root@localhost ~]# source /etc/profile
```



##### 	输入输出重定向

|    设备     | 文件描述符 |
| :---------: | :--------: |
| /dev/stdin  |     0      |
| /dev/stdout |     1      |
| /dev/strerr |     2      |



```
[root@localhost ~]# dasfas 2> error.txt		随便输了一个命令，错误信息保存到了文件中（前面必须加2）

[root@localhost dev]# dasdas &>> error.txt	不过大多数情况用这个


把正确结果输出到正确文件中，错误结果输出到错误文件中
[root@localhost dev]# ls >> log.txt 2>> error.txt	这个更常用

```

##### 	wc

​		-c		统计字节数	

​		-w		统计单词数

​		-l		统计行数

```
[root@localhost ~]# wc error.txt 
 1  5 33 error.txt

1:	此文件就一行内容
5:	此文件5个词
33:	此文件33个字节
```

**特殊用法**

```
[root@localhost ~]# wc << e
> 123
> 321
> e
2 2 8
```



##### 	$?

​		是一个变量，表示上一条命令是否执行成功0代表成功，非0代表失败



##### 	;

​		命令一起执行

##### 	&&

​		第一个命令执行成功，第二个才会执行

​		第一个命令执行失败，第二个就不会执行

##### 	||

​		第一个命令执行成功，第二个不会执行

​		只有第一个执行失败，第二个才会执行



#### 	bash中的其他符号

​	

|    符号    |                         说明                          |
| :--------: | :---------------------------------------------------: |
| ''(单引号) |          在单引号中的所有特殊符号都没有含义           |
| ""(双引号) |   双引号中只有$,`(反引号，引用系统命令),\有特殊含义   |
| ``(反引号) |        括起来的内容是系统命令(查看下面的3,4例)        |
|    $()     |                    跟上面一样(5例)                    |
|    （）    |         在小括号中执行的命令会在子shell中执行         |
|     {}     | 大括号中的命令会在当前shell执行，{1..10}可以用for遍历 |
|     #      |                         注释                          |
|     $      |                     取出变量的值                      |
|     \      |                        转义符                         |

```
1.
[root@localhost ~]# echo '$PATH'
$PATH	//单引号中的$PATH,没有任何含义，原样输出

2.
[root@localhost ~]# echo "$PATH"
#双引号中的PATH
/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

3.
[root@localhost ~]# echo `ls`
#反引号可以运行系统命令	
a anaconda-ks.cfg error.txt install.log install.log.syslog test.txt~ webmin-1.941 webmin-1.941_(1).tar.gz

4.
#反引号的应用
[root@localhost ~]# a=`date`
[root@localhost ~]# echo $a
2020年 01月 25日 星期六 22:23:41 CST

5.
#$()的应用
[root@localhost ~]# b=$(date)
[root@localhost ~]# echo $b
2020年 01月 25日 星期六 22:25:37 CST


```



#### 变量叠加

​	shell中的变量默认都是字符串类型的

```
[root@localhost ~]# a=hello
[root@localhost ~]# a="$a"world
[root@localhost ~]# echo $a
helloworld

```



#### 变量

##### 用户自定义变量

###### set

​	-u	输出没有定义的变量会报错(常用)

```
[root@localhost html]# set -u
[root@localhost html]# echo $abc
bash: abc: unbound variable
```

​	-x	在命令执行之前，回先把命令输出一遍

```
[root@localhost html]# set -x
++ printf '\033]0;%s@%s:%s\007' root localhost /var/www/html
[root@localhost html]# ls
+ ls --color=auto
index.html
++ printf '\033]0;%s@%s:%s\007' root localhost /var/www/html
```



###### env

​	查看一部分环境变量信息，不能查看到用户自定义的变量



##### unset

​	变量删除

​	可以删除系统环境变量

```
[root@localhost ~]# unset  name
```



##### 环境变量

​	在用户其子shell中也生效，这是用户自定义变量做不到的

###### export

​	声明用户自定义环境变量

​	其实是declare -x

```
[root@localhost ~]# export name='sjh'

#也可以这样
[root@localhost ~]# name=wh
[root@localhost ~]# export name

```



###### PS1命令提示符

​	这个变量主要是定义命令提示符的

```
[root@localhost ~]# echo $PS1
[\u@\h \W]\$
```

```
\d ：代表日期，格式为weekday month date
\H ：完整的主机名
\h ：主机的第一个名字
\t ：显示时间为24小时格式(HH:MM:SS)
\T ：显示时间为12小时格式
\A ：显示时间为24小时格式(HH:MM)
\u ：当前用户的账户名
\v ：BASH的版本信息
\w ：完整的工作目录名
\W ：利用basename取得工作目录名称，所以只会列出最后一个目录
# ：第几个命令
$ ：提示字符，如果是root时，提示符为：#;普通用户为：$

```

```
PS1='\u@[\e[1;93m]\h[\e[m]:\w$[\e[m]'	记得加单引号
```



###### LANG

​	系统语言环境变量

​	临时修改其值可以临时生效，永久修改需要修改/etc/sysconfig/i18n文件

###### locale

​	查看系统支持的语系

​	当前终端语系配置保存在/etc/sysconfig/i18n文件中

​	-a	查看所有

```
[root@localhost ~]# locale -a | grep en_US
en_US
en_US.iso88591
en_US.iso885915
en_US.utf8

```

```
[root@localhost ~]# cat /etc/sysconfig/i18n 
LANG="zh_CN.UTF-8"

```



##### 位置参数变量

| 参数 |                            说明                            |
| :--: | :--------------------------------------------------------: |
|  $n  | 0表示命令本身，1-9代表位置1-位置9的参数，10以后需要加${10} |
|  $*  |  这个变量代表命令行中的所有参数，把所有的参数看成一个整体  |
|  $@  |               也代表所有参数，只不过区分对待               |
|  $#  |                     代表所有参数的个数                     |

###### $n

```
[root@localhost test]# cat add.sh 
#!/bin/bash
a=$1
b=$2
echo $(($a+$b))		在shell中如果要想让两个数相加必须使两个数在$(())中

```



###### $*

```
[root@localhost test]# cat 02.sh 
#!/bin/bash
echo $*
[root@localhost test]# ./02.sh 1 2 3 4
1 2 3 4

```

###### $@

```

```



###### $#

```
[root@localhost test]# cat 03.sh 
#!/bin/bash
echo $#
[root@localhost test]# ./03.sh hello world
2	表示两个参数

```



##### 预定义变量

| 变量 |              解释               |
| :--: | :-----------------------------: |
|  $?  | 上次命令执行的成功与否0代表成功 |
|  $$  |          当前进程的PID          |
|  $!  |   后台运行的最后一个进程的PID   |

2020/1/30 10:00	双桥begin

##### read

​	-p	提示信息

​	-t	时间限制

​	-n	限制输入多少个字符

​	-s	隐藏输入

```
[root@localhost test]# cat add2.sh 
#!/bin/bash
read -t 30 -p 'please input a number: ' num1
read -t 30 -p 'please input a number: ' num2

echo $(($num1+$num2))

```



##### declare

​	声明变量

​	-a	声明数组

​	-i	声明整型变量

​	-x	声明为环境变量	export最终就是使用的declare -x

​	-p	打印所有声明的变量

​	-r	定义为只读



**两个字符串相加的结果声明成整型变量**

```
[root@localhost test]# a=1
[root@localhost test]# b=2
[root@localhost test]# declare -i c=$a+$b
[root@localhost test]# echo $c
3
```



```
[root@localhost test]# name[0]='sjh'
[root@localhost test]# name[1]='wh'
[root@localhost test]# echo ${name[*]}
sjh wh
[root@localhost test]# echo ${name[1]}
wh
[root@localhost test]# echo $name
sjh
```



##### 数值运算

###### 	declare

```
declare -i c=$a+$b
```

###### 	expr

```
[root@localhost test]# e=$(expr $a + $b)
[root@localhost test]# echo $e
3
```

###### 	let

```
[root@localhost test]# a=1
[root@localhost test]# b=2
[root@localhost test]# let c=a+b
[root@localhost test]# echo $c
3
[root@localhost test]# let d=$a+$b
[root@localhost test]# echo $d
3
```



###### 	$(())

```
[root@localhost ~]# a=2
[root@localhost ~]# b=2
[root@localhost ~]# c=$(($a+$b))
[root@localhost ~]# echo $c
4

```



###### 	$[]

```
[root@localhost ~]# d=$[$a+$b]
[root@localhost ~]# echo $d
4
```



##### 变量测试与内容置换

| 变量置换方式 |          y没有设置           |      y为空值       |     y有值     |
| :----------: | :--------------------------: | :----------------: | :-----------: |
| x=${y-新值}  |            x=新值            |       x为空        |     x=$y      |
| x=${y:-新值} |            x=新值            |       x=新值       |     x=$y      |
| x=${y+新值}  |            x为空             |       x=新值       |    x=新值     |
| x=${y:+新值} |            x为空             |       x为空        |    x=新值     |
| x=${y=新值}  |        x=新值，y=新值        |    x为空，y不变    |  x=$y，y不变  |
| x=${y:=新值} |        x=新值，y=新值        |   x=新值，y=新值   | x=$y，y值不变 |
| x=${y?新值}  | 新值输出到标准错误输出(屏幕) |       x为空        |     x=$y      |
| x=${y:?新值} |      新值输出到标准错误      | 新值输出到标准错误 |     x=$y      |



##### 环境变量配置文件

```
/etc/profile
/etc/profile.d/*
~/.bash_profile
~/.bashrc
/etc/bashrc
```



###### /etc/motd

​	登录后欢迎信息

​	直接编辑即可

```
[root@localhost ~]# cat /etc/motd 
Wraning!!!
如果你没有被授权，请离开
```

2020/1/30 11:45	end



##### 正则表达式
|  正则符   |                      作用                       |
| :-------: | :---------------------------------------------: |
|     ?     |      匹配前一个字符重复0次或1次(扩展正则)       |
|     *     |           匹配前一个字符0次或任意多次           |
|    []     |            匹配中括号中任意一个字符             |
|    [-]    |               -代表一个范围[a-z]                |
|    [^]    | 逻辑非，[ ^0-9 ]就是匹配一个不是数字的字符(1例) |
|     ^     |                    匹配行首                     |
|     $     |                    匹配行尾                     |
|     .     |          匹配除了换行符外任意一个字符           |
|  \\{n\\}  |              匹配前面的字符正好n次              |
| \\{n,\\}  |          匹配前面的字符不小于n次(>=n)           |
| \\{n,m\\} |     匹配前面的字符n-m次(大于等于n小于等于m)     |
|     +     |   匹配前一个字符一次或任意多次(拓展正则)(2例)   |
|    \|     |                     或(4例)                     |
|    ()     |            将包住的内容看成整体(3例)            |

上面的大括号为什么要加\?，因为要将其转义，并且只在基础正则中需要加，拓展正则不需要加

```
1例
#匹配此文件中没有sad任何一个字母的行
[root@localhost test]# grep [^sad] test.txt 

2例
go+gle	能匹配到gogle google gooogle当然如果o还有更多也可以匹配到

3例
(dog)+	匹配dog，dogdog，dogdogdog......

4例
[root@localhost test]# cat test1.txt 
hello
world
[root@localhost test]# egrep "(hello|world)+" test1.txt 
hello
world

```



##### cut

​	不识别空格，只识别制表符

​	-f	提取第几列

```
[root@localhost test]# cat student.txt 
ID	NAME	gender	Mark
1	SJH	MALE	NIGHTKING
2	WH	FEMALE	MYLADY
3	NOONE	UNKNOW	UNKNOW
[root@localhost test]# cut -f 2 student.txt 
NAME
SJH
WH
NOONE

```



​	-c	按照字符提取

```
[root@localhost test]# cut -c 8- student.txt 
	gender	Mark
ALE	NIGHTKING
MALE	MYLADY
	UNKNOW	UNKNOW


这啥意思呢?是从每一行的第八个字符到最后一个字符
1-5	每一行的1-5个字符
-5	每一行的前5个字符
```

​	-d	指定分隔符

```
[root@localhost test]# cut -d : -f 1 /etc/passwd
```



#### awk

​	-v	声明变量

​	是打印文本的，可以按照自己想要的格式打印文本

​	**awk '条件1{动作1} 条件2{动作2}'**

​	注意:**$0**是打印整行数据，以后你肯定会有疑问，不过这句话是正确的，好好想吧



###### 	printf

​		![58242924408](H:\笔记\images\1582429244081.png)

###### 三目运算符

```
[root@chuquwan6 ~]# awk -v FS=":" '{$3>500?ut="普通用户":ut="系统用户";print ut}' /etc/passwd 
```





###### Demo

```
打印student.txt的第2列和第3列

[root@localhost test]# cat student.txt 
ID	NAME	gender	Mark
1	SJH	MALE	NIGHTKING
2	WH	FEMALE	MYLADY
3	NOONE	UNKNOW	UNKNOW
[root@localhost test]# awk '{printf $2 "\t" $3"\n"}' student.txt 
NAME	gender
SJH	MALE
WH	FEMALE
NOONE	UNKNOW


打印df -h命令的第5列，得到分区空间使用信息

[root@localhost test]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda5        16G  2.4G   13G  16% /
tmpfs           490M     0  490M   0% /dev/shm
/dev/sda1       190M   34M  147M  19% /boot
/dev/sda3       1.9G  3.1M  1.8G   1% /home
[root@localhost test]# df -h | awk '{printf $5"\n"}'
Use%
16%
0%
19%
1%


得到根分区的使用率信息

[root@localhost test]# df -h | grep '/$' --color=auto
/dev/sda5        16G  2.4G   13G  16% /
[root@localhost test]# df -h | grep '/$' | awk '{print $5}'
16%
[root@localhost test]# df -h | grep '/$' | awk '{print $5}'
16%
[root@localhost test]# df -h | grep '/$' | awk '{print $5}' | cut -d '%' -f 1
16
[root@localhost test]# df -h | grep '/$' | awk '{print $5}' | cut -d '%' -f 2

[root@localhost test]# df -h | grep '/$' | awk '{print $5}' | cut -d '%' -f 1
16


```



###### BEGIN

​	表示在执行之前先执行BEGIN后面的语句

```
[root@localhost test]# awk 'BEGIN{print "名字\t标记"} {print $2"\t" $4}' student.txt 
名字	标记
NAME	Mark
SJH	NIGHTKING
WH	MYLADY
NOONE	UNKNOW

```



###### END

​	表示在执行之后执行END后面的语句



##### 条件

|    运算符     |             意义             |
| :-----------: | :--------------------------: |
|       >       |                              |
|      >=       |                              |
|       <       |                              |
|      <=       |                              |
|      !=       |                              |
|      ==       |                              |
|      A~B      |      字符串A包含字符串B      |
|     A!~B      |     字符串A不包含字符串B     |
|   /pattern/   | 匹配到pattern的行（下面例1） |
| /pat1/,/pat2/ |     从pat1到pat2中间的行     |
| NR>=n&&NR<=m  |           从n-m行            |

```
例1

#打印以UUID开头的行
[root@chuquwan6 ~]# awk '/^UUID/{print $1}' /etc/fstab 
UUID=9113c69a-d7f3-4efa-a651-0cc319572ae0
UUID=4aa7bf08-6c47-4559-b9f0-5a186a763046
UUID=9ff3b784-d835-40a9-a669-d9e1301828fb
UUID=f3aad8aa-e336-4c75-8e90-9ce3f4566854

#打印不是UUID开头的行
[root@chuquwan6 ~]# awk '!/^UUID/{print $1}' /etc/fstab 

#
#
#
#
#
#
#
tmpfs
devpts
sysfs
proc

```



###### 	>=

```
[root@localhost test]# cat student.txt 
ID	NAME	gender	Mark
1	SJH	MALE	NIGHTKING
2	WH	FEMALE	MYLADY
3	NOONE	UNKNOW	UNKNOW
[root@localhost test]# awk '$1>=2{print $2}' student.txt 
NAME
WH
NOONE

```



###### 	~包含

###### 	/正则/

```
[root@localhost test]# cat student.txt 
ID	NAME	gender	Mark
1	SJH	MALE	NIGHTKING
2	WH	FEMALE	MYLADY
3	NOONE	UNKNOW	UNKNOW
[root@localhost test]# awk '$2 ~ /SJ/{print $4}' student.txt 
NIGHTKING
[root@localhost test]# awk '$2 ~ /SJH/{print $4}' student.txt 
NIGHTKING
[root@localhost test]# awk '$2 ~ /SJHk/{print $4}' student.txt 

```



##### awk内置变量

| 变量 |                   作用                    |
| :--: | :---------------------------------------: |
|  $0  |               代表整行数据                |
|  $n  |               代表第n个字段               |
|  NF  |           当前行拥有的字段总数            |
|  NR  |         当前awk所处理的行是第几行         |
|  FS  |                指定分隔符                 |
| ARGC |              命令行参数个数               |
| ARGV |              命令行参数数组               |
| FNR  | 当前文件中的当前记录数(对输入文件起始为1) |
| OFMT |        数值的输出格式(默认为%.6g)         |
| OFS  |       输出字段的分隔符(默认为空格)        |
| ORS  |       输出行的分隔符(默认为换行符)        |
|  RS  |       输入行的分隔符(默认为换行符)        |



###### FS

​	指定分隔符

```
打印新用户的名字

[root@localhost test]# grep '/bin/bash$' /etc/passwd | grep -v '^root' | awk 'BEGIN{FS=":"} {print $1}'
sjh
user1
user2
user3

awk -v FS=":"

```

###### NR&NF

```
[root@localhost ~]# grep '/bin/bash$' /etc/passwd | awk 'BEGIN{FS=":"} {print $1 "\t第" NR "行\t" NF "个字段"}'
root	第1行	7个字段
sjh	第2行	7个字段
user1	第3行	7个字段
user2	第4行	7个字段
user3	第5行	7个字段

```

###### OFS

​	指明输出字段分割符

```
[root@chuquwan6 ~]# tail -5 /etc/shadow | awk 'BEGIN{FS=":";OFS="  --  "}{print $1,$2}'
sjh  --  $6$o2PsAv3Z$PFqKkfYSmxKsyi8S8w5RcfXsCjGP7evcz0BWW2KPHiV888dRjsgHn/DTnaFvbwkt4G0L5VcLV8/NVqks7vQiE1
apache  --  !!
user1  --  $6$hUSx4wau$EL2UmY5nEgojgHsyMsnWGygMGgRllTRUp2h0cEm7Ba1PmRhMtfLZcI8iOpOyVVivnoNieyT6seTIwvVDiTnrw1
user2  --  !!
user3  --  !!

```







##### awk自定义变量

​	使用awk -v选项自定义变量

##### 控制语句

​	![58243721444](H:\笔记\images\1582437214447.png)

###### 	if else

```
#/etc/passwd文件中UID大于500打印普通用户，否则打印系统用户
[root@chuquwan6 ~]# awk -v FS=":" '{if($3>500){print "普通用户",$1}else {print "系统用户",$1}}' /etc/passwd
```

###### 	while

```
#/etc/passwd文件中打印每个字段的内容和字段长度
[root@chuquwan6 ~]# awk -v i=1 -F :  '{i=1;while(i<=NF){printf $i"\t"length($i)"\n";i++}}' /etc/passwd
```





​	























#### sed

​	Stream Editor

​	每读一行，将此行放入pattern space，如果与条件匹配，就执行后面的方法

​	如果不匹配，就不会执行后面的方法

​	执行到最后，如果没有加-n，那么就打印此pattern space，并清空pattern space

​	**地址定界**:

​		n:				单行

​		/pattern/:		被此正则模式匹配到的行

​		n,m:			n-m行

​		n,+x:			n行和它之后的x行	5，+2就是5,7

​		/pat1/,/pat2/:	第一次被pat1匹配到的行到第一次被pat2匹配到的行

​		n,/pat1/:			第n行到被第一次pat1匹配到的行

​		n~m			步进，例:

```
sed '1~2p' student.txt		打印奇数行
```





​	**替换标记**

​		g			本行匹配到的全部替换

​		p			替换后的结果打印输出

​		w	path	将替换后的结果写入文件

​		



​	用来将数据进行选取，替换，删除，新增的命令

​	-r	支持拓展正则

​	-n	quiet	只输出经过sed命令处理过的数据

```
[root@localhost test]# cat student.txt 
ID	NAME	gender	Mark
1	SJH	MALE	NIGHTKING
2	WH	FEMALE	MYLADY
3	NOONE	UNKNOW	UNKNOW
[root@localhost test]# sed -n '2p' student.txt 	//只打印第二行
1	SJH	MALE	NIGHTKING

```

​	-i	将修改后的内容写入文件

​	-e	支持多个操作

​	**其实直接加分号就可以了，不用加-e**

```
[root@localhost test]# sed -i -e '3d;5d' student.txt 
删除文件的第3行和第5行并写入
```





|  p   |                          打印                           |
| :--: | :-----------------------------------------------------: |
|  a   |                  追加(在行的后面)(例1)                  |
|  i   |                  插入(在行的前面)(例2)                  |
|  d   |                          删除                           |
|  a\  |                    插入两行数据，例3                    |
|  c   |                       行替换(例4)                       |
|  s   |                     字符串替换(例5)                     |
|  w   |         将当前pattern中的内容写入一个文件(例6)          |
|  =   |               打印行号(打印在上面)（例8）               |
|  r   | 将指定文件内容读取到匹配的行下(都是读到了模式空间)(例7) |
|  ！  |       对条件取反（是对范围匹配的条件取反）（例9）       |
|  &   |                 引用前面匹配到的(例10)                  |

```
例1	-a追加数据

[root@localhost test]# sed '2,4a hello' student.txt 
ID	NAME	gender	Mark
1	SJH	MALE	NIGHTKING
hello
2	WH	FEMALE	MYLADY
hello
3	NOONE	UNKNOW	UNKNOW
hello
[root@localhost test]# sed -n '2,4a hello' student.txt 
hello
hello
hello


例2	-i插入数据

[root@localhost test]# sed '2,4i world' student.txt 
ID	NAME	gender	Mark
world
1	SJH	MALE	NIGHTKING
world
2	WH	FEMALE	MYLADY
world
3	NOONE	UNKNOW	UNKNOW


例3	插入两行数据

[root@localhost test]# sed '2i hello \
> world ' student.txt
ID	NAME	gender	Mark
hello 
world 
1	SJH	MALE	NIGHTKING
2	WH	FEMALE	MYLADY
3	NOONE	UNKNOW	UNKNOW

例4	行替换

#将第二行数据替换为No Such Person
[root@localhost test]# sed -i '2c No Such Person' student.txt


例5	字符串替换

[root@localhost test]# cat sed.txt 
wh
wh
sjh
sjh
[root@localhost test]# sed '1,2s/wh/whwoaini/g' sed.txt 	替换1,2行的wh，如果不写，就是全局
whwoaini
whwoaini
sjh
sjh

#执行多条替换用分号隔开
[root@localhost test]# sed '1,2s/wh/whwoaini/g;3,4s/sjh/sjhwoaini/g' sed.txt 
whwoaini
whwoaini
sjhwoaini
sjhwoaini

#行首加#号
[root@localhost test]# sed 's/^/#/g' sed.txt 
#wh
#wh
#sjh
#sjh

例6
#把student.txt的第一行写入test.txt
#以后还是加单引号
[root@chuquwan6 test]# sed '1w test.txt' student.txt 


例7
#将/etc/fstab文件中的内容读到student.txt的第一行下面
[root@chuquwan6 test]# sed -n '1r /etc/fstab' student.txt 

#
# /etc/fstab
# Created by anaconda on Wed Dec 25 05:52:42 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=9113c69a-d7f3-4efa-a651-0cc319572ae0 /                       ext4    defaults        1 1
UUID=4aa7bf08-6c47-4559-b9f0-5a186a763046 /boot                   ext4    defaults        1 2
UUID=9ff3b784-d835-40a9-a669-d9e1301828fb /home                   ext4    defaults        1 2
UUID=f3aad8aa-e336-4c75-8e90-9ce3f4566854 swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0


例8
#等号显示行号
[root@chuquwan6 test]# sed -n '/^/=' /etc/fstab 
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16


例9
#不是以UUID开头的行打印
1!p	就是打印不是第一行的行
[root@chuquwan6 test]# sed -n '/^UUID/!p' /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Wed Dec 25 05:52:42 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0

例10
#字符串替换
[root@chuquwan6 test]# sed 's/root/&er/g' /etc/passwd
rooter:x:0:0:rooter:/rooter:/bin/bash


```

###### sed例子

```
删除/boot/grub/grub.conf所有以空白开头的行行首的空白字符

[root@chuquwan6 test]# sed 's/[[:space:]]\+//g' /boot/grub/grub.conf 

删除/etc/fstab文件中所有以#开头，后面至少跟了一个空白字符的行的行首的#和空白字符

[root@chuquwan6 test]# sed 's/^#[[:space:]]\+//g' /etc/fstab 

echo一个绝对路径给sed，取出其基名，取出其目录名
[root@chuquwan6 test]# echo /etc/sysconfig | sed 's@[^/]\+/\?$@@'
/etc/
```

###### sed高级

![58191691280](H:\笔记\images\1581916912806.png)

![58192159391](H:\笔记\images\1581921593915.png)

#### sort

​	排序命令，默认以第一行的第一个字符进行排序

```
sort /etc/passwd
```

​	-t	指定分隔符

​	-k	指定字段号

​	-n	以数字来排序，默认是以字符串形式排序

​	-u	取消重复行

​	-r	降序排序



```


[root@localhost test]# sort -n -t ":" -k 3,3 /etc/passwd	

[root@localhost test]# sort -n -t ":" -k 3 /etc/passwd
```



**3,5表示如果第3列一样的话，就排第4列，直到第五列**





### Shell条件判断

​	**使用[]作为判断的操作符**

#### 	文件判断

| 选项 |           说明           |
| :--: | :----------------------: |
|  -b  |  是否存在且是块设备文件  |
|  -c  | 是否存在且是字符设备文件 |
|  -d  |     是否存在且为目录     |
|  -e  |         是否存在         |
|  -f  |   是否存在且为普通文件   |
|  -L  | 是否存在且为符号链接文件 |
|  -p  |   是否存在且为管道文件   |
|  -s  |      是否存在且非空      |
|  -S  |  是否存在且为套接字文件  |



| 选项 |              说明              |
| :--: | :----------------------------: |
|  -r  | 文件是否拥有读权限(只要有就行) |
|  -w  |          是否有写权限          |
|  -x  |         是否有执行权限         |
|  -u  |           是否有SUID           |
|  -g  |           是否有SGID           |
|  -k  |          是否有黏着位          |



|      选项       |                     说明                      |
| :-------------: | :-------------------------------------------: |
| 文件1 -nt 文件2 |         文件1比文件2新为真(修改时间)          |
| 文件1 -ot 文件2 |              文件1比文件2旧为真               |
| 文件1 -ef 文件2 | 文件1和文件2的inode号是否一样(用于判断硬链接) |



##### test

```
[root@localhost test]# test -f test.txt 
[root@localhost test]# echo $?
0

[root@localhost test]# [ -f test.txt ]
[root@localhost test]# echo $?
0
```



#### 整数判断

|      选项       |  说明  |
| :-------------: | :----: |
| 整数1 -eq 整数2 |  相等  |
| 整数1 -ne 整数2 | 不相等 |
| 整数1 -gt 整数2 |  1>2   |
| 整数1 -lt 整数2 |  1<2   |
| 整数1 -ge 整数2 |  1>=2  |
| 整数1 -le 整数2 |  1<=2  |

#### 字符串的判断

|        选项        |          说明          |
| :----------------: | :--------------------: |
|     -z 字符串      | 字符串是否为空(空为真) |
|     -n 字符串      |    字符串是否为非空    |
| 字符串1 == 字符串2 |                        |
| 字符串1 != 字符串2 |                        |



#### 逻辑判断

| 选项 | 说明 |
| :--: | :--: |
|  -a  | and  |
|  -o  |  or  |
|  !   |  非  |

```

#此文件存在并且是普通文件
[root@localhost test]# [ -e /root/test/1 -a -f /root/test/1 ]
[root@localhost test]# echo $?
0

#此文件不是目录
[root@localhost test]# [ ! -d /root/test/1  ]	!后面一定要加空格哦
[root@localhost test]# echo $?
0

[root@localhost test]# a=10
[root@localhost test]# [ -n $a ]	a是不是非空
[root@localhost test]# echo $?
0
[root@localhost test]# [ -n $a -a $a -gt 8 ]	a非空且大于8
[root@localhost test]# echo $?
0


```



#### if

​	if可以判断命令是否执行成功

```
 #!/bin/bash
  
 read -p "输入一个数字" num1
  
 if grep '^[[:digit:]]*$' <<< "$num1" &>> /dev/null
         then
                 echo "is a number"
 else
         echo "not a number"
 fi
  

```



##### nmap

​	扫描指定主机的常见服务是否启动，可以使用它输出的信息判断Apache服务是否启动

​	默认没有安装

```
yum -y install nmap
```

```
[root@localhost Packages]# nmap -sT 127.0.0.1

Starting Nmap 5.51 ( http://nmap.org ) at 2020-02-02 09:47 CST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00026s latency).
Not shown: 995 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
25/tcp  open  smtp
80/tcp  open  http
111/tcp open  rpcbind
631/tcp open  ipp

Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds

```



##### if then

```
if [ 条件判断 ];then
	程序
fi

if [ 条件判断 ]
	then
		程序
fi

```

**监测系统根分区的占用情况**

```
[root@localhost test]# cat if1.sh 
#!/bin/bash
a=$(df -h | grep '/$' | awk '{print $5}' | cut -d % -f 1)
echo $a
if [ $a -gt 80 ];then
	echo "系统空间即将用尽"
	else
	echo "系统空间暂时充足"
fi

```



##### if else

```
if [ 判断条件 ]
	then
		程序
else
	程序
fi
```



##### if elif

```
if [ 判断条件 ]
	then
		语句
elif [ 判断条件 ]
	then
		语句
elif [ 判断条件 ]
	then
		语句
else
	语句
fi
```



#### case

```
#!/bin/bash

echo "Please input 1 if you want to beijing"
echo "Please input 2 if you want to shanghai"
echo "Please input 0 if you want to NewYork"

read -p "Please input your choise : " choise

case $choise in
	"0" )
		echo "go to NewYork"
		;;
	"1" )
		echo "go to beijing"
		;;
	"2" )
		echo "go to shanghai"
		;;
	* )
		echo "go to nowhere"
		;;
esac

```



#### for

##### 第一种

```
#!/bin/bash

for i in 1 2 3
	do
		echo $i
	done
```

##### 第二种

```
#!/bin/bash

for (( i=1;i<=100;i=i+1))
	do
		echo $i
	done
```

```
#!/bin/bash

num=$(cat /root/file.txt | wc -l)

for(( i=0;i<$num;i=i+1))
	do
		awk 'NR=='$i'{print $1}' /root/file.txt
	done

```



###### 过滤ip地址正则表达式

```
"^(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|[1-9])\\."

+"(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)\\."

+"(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)\\."

+"(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)$"
```



#### while

```
while [ 条件表达式 ]
	do
		程序
	done
	
	

#!/bin/bash

i=0
while [ $i -lt 100 ]
	do
		echo $i
		i=$(($i+1))
	done

```

#### until

​	只有到条件表达式成立才会终止循环

```
until [ 条件表达式 ]	
	do
		程序
	done
```

#### exit

```
exit 10

```



判断是不是数字的脚本

```
[root@localhost test]# cat isnumber.sh 
#!/bin/bash

read -p "please input: " number

if egrep "^[1-9][0-9]*\.?[0-9]*$" <<< $number
	then
	echo "is a number"
else
	echo "not a number"
fi

```



#### function

```
function func_name {
    ...
}

func_name() {
    ...
}

调用直接写名字即可，不用加小括号
```

![58199684899](H:\笔记\images\1581996848999.png)



#### 数组

##### 普通数组

###### 声明数组

​	因为是弱类型，不声明也可以直接赋值

```
declare -a arr

#关联数组：相当于map
declare -A arr
```

###### 赋值

```
arr[0]="wh"

arr=("wh" "sjh"......)	中间不能加逗号

arr=([0]="wh" [1]="sjh" [7]="")下标可以是分散的
```



###### 数组长度

```
echo ${arr[*]} | wc -w

[root@chuquwan6 test]# echo ${arr[*]}
wh sjh


[root@chuquwan6 test]# echo ${#arr[*]}
8
```

###### 删除数组元素

```
unset arr[1]	删除数组索引为1的元素
```



###### 数组切片

​	${arr[*]:offset:number}

​		offset:			跳过几个元素

​		number:			取几个

​	因为弱类型的shell数组下标有可能不连续，这个时候如果要取数组中间的一部分元素就比较棘手

```
[root@chuquwan6 test]# echo ${arr[*]}
1 2 3 4 5 6 7 8
[root@chuquwan6 test]# echo ${arr[*]:2:3}
3 4 5
[root@chuquwan6 test]# echo ${arr[*]:2}
3 4 5 6 7 8

```



##### 关联数组(map)

###### 定义

```
declare -A arr=([k1]="v1" [k2]="v2" ...)
下面这样定义不可以，必须加-A
arr=([k1]="v1" [k2]="v2" ...)


关联数组中也可以存储数字索引，但是普通数组中不可以存储关联数组中的键值对
arr=([key2]="vlaue2" [key1]="value1" [0]="sjhaiwanghuan" [1]="sjh" [2]="wh" )
```





##### $RANDOM

​	随机数，是一个环境变量

​	0-32767



#### 字符串处理

##### 切片

​	${var:offset:number}

​		offset:			跳过几个元素

​		number:			取几个

```
[root@chuquwan6 test]# name=sjhaiwanghuan
[root@chuquwan6 test]# echo ${name:3}
aiwanghuan
```

##### 取最右边的几个字符

​	${var: -n}	必须加空格

```
[root@chuquwan6 test]# echo $name
sjhaiwanghuan
[root@chuquwan6 test]# echo ${name: -8}
wanghuan
```

##### 奇怪用法之取子串

​	**注意：奇怪用法中的pattern不是正则**

###### ${var#*pattern}

​	从最左侧查找var变量，找到第一次匹配到pattern的地方，和pattern一起删除

```
[root@chuquwan6 test]# echo $name
sjhaiwanghuan

#第一次匹配到i并将其和前面的删除

[root@chuquwan6 test]# echo ${name#*i}
wanghuan
```



###### ${var##*pattern}

​	自左而右，查找最后一个匹配到pattern的地方并将其和之前的内容删除

```
[root@chuquwan6 test]# path="/var/log/message"

#取基名
[root@chuquwan6 test]# echo ${path##*/}
message

```



###### ${var%pattern*}

​	自右向左，找到第一次匹配到pattern的地方并将其和之前的内容删除

###### ${var%%pattern*}	

​	自右向左，查找最后一个匹配到pattern的地方并将其和之前的内容删除



##### 奇怪用法之查找替换

###### ${var/pattern/replace}

​	查找var所查找的字符串中**第一次**匹配到pattern的地方，并以replace替换

###### ${var//pattern/replace}

​	查找var所查找的字符串中**全部**匹配到pattern的地方，并以replace替换

###### ${var/#pattern/replace}

​	pattern必须出现在**行首**

###### ${var/%pattern/replace}

​	pattern必须出现在**行尾**



##### 字符串大小写转换

###### ${var^^}

​	把字符串中的所有小写字母转换成大写

###### ${var,,}

​	把字符串中的所有大写字母转换成小写



