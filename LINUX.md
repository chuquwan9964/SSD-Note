# LINUX

## 基本命令

#### 目录文件操作命令

##### ls	-[选项] -[参数]

选项:

​	-a	显示所有文件，包括隐藏文件

​	-i	显示iNode节点号

​	-l	长格式显示数据

​	-h	以人类善于读懂的方式显示数据

​	--color=always，never，auto	是否显示颜色

​	-d	显示目录信息

##### mkdir	-[选项] -[参数]

选项：

​	-p	可以创建多级目录

​	-v	显示过程

细节：

​	如果当前目录下已存在同名目录，报错



##### rmdir	-[选项] -[参数]

**rmdir - remove empty directories**

选项:

​	-p	太笨了，没啥用

​	-v	显示过程

##### touch	-[选项] -[参数]

​	摸一下文件，如果不存在则创建，如果存在则修改文件的时间戳

##### stat		-[选项] -[参数]

​	查看文件信息的命令

```
  File: "abc"
  Size: 2         	Blocks: 8          IO Block: 4096   普通文件
Device: 805h/2053d	Inode: 791277      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-12-27 03:31:00.090912352 +0800	最近一次访问时间
Modify: 2019-12-27 03:30:55.979905907 +0800	最近一次修改数据时间
Change: 2019-12-27 03:30:55.979905907 +0800	最近一次修改状态时间（所属组，权限等）

```

**Linux没有创建时间!!!**

##### cat		-[选项] -[参数]

**cat - concatenate files and print on the standard output**

​	选项

​	-n	显示行号

​	-A	显示隐藏符号	相当于-vET

```
[root@localhost tmp]# cat -A abc 	//abc文件中只写了一个1
1$

```

​	-v	列出特殊字符

​	-E	列出每行结尾的结束符$

​	-T	把Tab键用^I显示出来

##### more	-[选项] -[参数]

分屏显示文件的命令

​	进入界面后

空格	下一页

b		上一页

回车	下一行

/		查找内容，没有高亮显示，不方便

##### less		-[选项] -[参数]

​	作用与more相似，这里不做讲解

##### head	-[选项] -[参数]

​	显示文件开头的n行信息，默认10行

​	-n	指定head的显示行数

​	也可以直接	head -10

##### tail		-[选项] -[参数]

​	显示文件末尾的n行信息，默认10行

​	-n	指定显示行数

​	也可以直接tail -10

​	-f	跟踪文件，会进入阻塞状态，当文件更新后显示更新后的内容

##### ln		-[选项] -[参数]

​	**文件的文件名和iNode号是保存在文件的父目录的block块中，而根目录的iNode号是固定的，iNode为2，所以当要找文件的内容时，必须先找到此文件的iNode号，然而此文件的iNode号是在父目录的block块中存储的，所以就要找到父目录的iNode号，层层往上找，直到找到根**



***硬链接与软连接***

- ​	**硬链接**

  ​	ln	abc	/tmp/abc_hard		创建abc的硬链接abc_hard

  注意事项：

  ​	硬链接文件使用的是相同的iNode，因此改变任意一个文件其他硬链接文件都会改变

  ​	硬链接会增加文件的引用计数，删除一个文件不会影响它的硬链接文件

  ​	硬链接不能链接目录，因为如果要链接目录，就要硬链接目录下的所有子目录及文件，代价太大

  ​	硬链接不能跨分区使用

  ​	硬链接标记不清，很难确定这是一个硬链接，只能根据iNode号来区分，不建议使用


- ​        **软连接**

  ​	ln	-s	/root/abc	/tmp/abc_s

  ​	软连接的两个文件拥有不同的iNode号，不同的block

  ​	修改任何一个文件都会导致另一个文件的变化

  ​	软连接的block块中只是保存了源文件的路径信息，因此必须写绝对路径

  ​	软连接可以跨分区，可以连接目录

  ​	软连接的权限为rwxrwxrwx，是否就以为这源文件可以随便访问？

  ​		不是！具体权限还是得看源文件的权限

  ​	删除源文件软连接无法使用，删除软连接源文件可以正常使用


	#### rm		-[选项] -[参数]
	
		-f	force	强制删除
		
		-i	interactive	交互式删除，会询问用户
		
		-r	recursive	递归删除

##### cp		-[选项] -[参数]
##### cp		-[选项] -[参数]
##### cp		-[选项] -[参数]

​	-a	相当于-rpd	保留源文件的所有属性(时间戳，软连接，属主属组)

​	-r/R	递归

​	-p	保留全部属性

​	-d	保留链接(如果文件为软连接，则复制出的文件也是软连接，**对硬链接无效**)

​	-i	交互	**默认就是交互的**(cp文件时目标目录存在同名文件，就会询问是否覆盖)

​	-n	不要覆盖已存在的文件

​	-f	强制覆盖

**直接运行cp命令其实是运行的cp命令的别名，cp -i	默认就是交互**



##### mv		-[选项] -[参数]

​	-f	强制覆盖，如果目标文件已存在

​	-i	交互，默认选项

​	-v	显示过程

**mv命令没有-r选项，如果要剪切文件夹的话，不需要加其他选项**



##### linux常见文件类型

| 文件类型 |         类型描述         |
| :------: | :----------------------: |
|    -     |         普通文件         |
|    d     |         目录文件         |
|    c     | 字符设备文件(键盘，鼠标) |
|    b     |     块设备文件(硬盘)     |
|    p     |         管道文件         |
|    s     |        套接字文件        |
|    l     |        软连接文件        |
|          |                          |

##### chmod		-[选项] -[参数]

**chmod - change file mode bits**

```
chmod +w abc	//给所有用户在abc文件增加写权限
chmod +r abc
chmod +x abc
chmod u+r|w|x
chmod g+r|w|x
chmod o+r|w|x
chmod a+r|w|x

chmod 644 abc	//给abc文件使用644权限
```

**普通用户可以修改属于自己的文件的权限**

##### chown		-[选项] -[参数]

-r	递归

```
chown user1:user1	abc	//给abc更改用户和组的信息(前面是用户，后面是组)
```

**普通用户无法修改文件的所有者，即使文件的所有者是自己**



##### 权限对于文件和目录的限制

|      | 文件           | 目录                                           |
| ---- | -------------- | ---------------------------------------------- |
| 读   | 表示文件可以读 | 表示目录可以使用ls命令，但必须配合执行权限     |
| 写   | 文件可以写     | 目录可以删除，修改，拷贝，剪切里面的文件或目录 |
| 执行 | 文件可以执行   | 目录可以cd进去                                 |
|      |                |                                                |

##### umask

​	umask是系统的默认权限，UID大于199的用户的umask是0002，小于的是0022，root就是0022

​	系统默认创建文件所能拥有的最大权限是666，目录是777，因此如果使用root用户创建文件的话，文件默认的权限是644，目录默认的权限是755

​	如何计算?

​		如果umask的值是022，那么当创建一个文件时，我们可以这样计算这个文件的权限:

​			**文件的默认创建权限是666，既	rw-rw-rw-，然后使用权限对位相减，**

**既rw-	rw	-rw-减去---	-w-	-w-，得到rw-	r--	r--**

**如果umask是033，那么rw-	rw	-rw-		减去---	-wx	-wx	得到rw-  r--  r--**

umask在**/etc/profile**定义，可以永久修改

##### whereis	查看指定命令位置，会显示命令位置及其帮助文档位置

##### which	查看命令位置及别名信息

##### whatis	查看命令是什么

```
[root@localhost ~]# whatis ls
ls                   (1)  - list directory contents
ls                   (1p)  - list directory contents

```



##### whoami	查看我是谁

##### type		查看命令是内部还是外部

```
[root@localhost ~]# type mkdir
mkdir is hashed (/bin/mkdir)	//外部命令，只要有路径

[root@localhost ~]# type cd
cd is a shell builtin		//shell内置命令


```

##### locate	查询文件位置(不是精准文件名查找)

根据数据库查询，数据库位置在	**/var/lib/mlocate/mlocate.db **

因为数据库不是实时更新的，所以当新建文件后会不一定找到，所以需要手动更新数据库

**updatedb**

**locate配置文件位置/etc/updatedb.conf ，内容为下**

```
PRUNE_BIND_MOUNTS = "yes"	//开启搜索限制，让这个配置文件生效

//排除以下文件系统不扫描
PRUNEFS = "9p afs anon_inodefs auto autofs bdev binfmt_misc cgroup cifs coda configfs cpuset debugfs devpts ecryptfs exofs fuse fusectl gfs gfs2 gpfs hugetlbfs inotifyfs iso9660 jffs2 lustre mqueue ncpfs nfs nfs4 nfsd pipefs proc ramfs rootfs rpc_pipefs securityfs selinuxfs sfs sockfs sysfs tmpfs ubifs udf usbfs"

//排除指定后缀名的文件
PRUNENAMES = ".git .hg .svn"

//排除以下目录不扫描
PRUNEPATHS = "/afs /media /net /sfs /tmp /udev /var/cache/ccache /var/spool/cups /var/spool/squid /var/tmp"	
```

##### find		查询文件位置

**find	搜索路径	[搜索选项]	搜索内容**

选项:

​	**-name**	根据文件名查找

```
[root@localhost ~]# find . -name abc
./abc
```



​	**-iname**	不区分文件名大小写

```
[root@localhost ~]# find . -iname abc
./abc
./ABC
```



​	**-inum**	根据iNode查询，可以通过此选项查找硬链接

```
[root@localhost ~]# find . -inum 663341
./abc
```

​	**-size	[+|-]**	根据文件大小搜索，+代表搜索n大的，-代表搜索比n小的

```
[root@localhost ~]# find . -size 12c
./abc
在当前目录下查询大小为12个字节的文件
```



```
-size n[cwbkMG]
              File uses n units of space.  The following suffixes can be used:
			//默认搜索单位，如果是find . -size 10	那就是在当前目录下搜索大小为10*512字节的文件
              ‘b’    for 512-byte blocks (this is the default if no suffix is used)
			//字节单位
              ‘c’    for bytes

              ‘w’    for two-byte words

              ‘k’    for Kilobytes (units of 1024 bytes)

              ‘M’    for Megabytes (units of 1048576 bytes)

              ‘G’    for Gigabytes (units of 1073741824 bytes)


The size does not count indirect blocks, but it does count blocks in sparse files that
are not actually allocated.  Bear in mind that the ‘%k’ and ‘%b’ format specifiers  of
-printf  handle  sparse  files  differently.   The  ‘b’ suffix always denotes 512-byte
blocks and never 1 Kilobyte blocks, which is different to the behaviour of -ls.
```

​	**-atime     [+|-]**		按照文件访问时间来查找

​	**-mtime     [+|-]**	按照文件数据修改时间来查找

```
[root@localhost ~]# find . -mtime -1	//搜索修改时间为1天内的文件
.
./ABC
./.Xauthority
./.lesshst

```



​	**-ctime    [+|-]**		按照文件的状态修改时间来查找

​	**三个默认都是以天为单位**

​	**-5代表五天内**

​	**+5代表6天前**

​	**5就代表5~6天之间**



**-perm**

```
find . -perm 644	查找文件权限为644的文件
```



```
-rw-r--r--. 1 root root 0 1月  14 03:37 file1
-rw-------. 1 root root 0 1月  14 03:37 file2

find . -perm +444

.
./file1
./file2


find . -perm -444

.
./file1

file1的权限是644，file2的权限是600
find . -perm +444此命令只要是有任何一个大于等于4到就能找到
find . -perm -444此命令必须三个都大于或等于4，才可以找到

```



**-user**

根据属主的名称来查询

```
find . -user root	在当前目录下查找属主为root的文件
```



**-group**

根据属组的名称来查询

```
find . -group root	在当前目录下查找属组为root的文件
```

-**nouser**

查询没有属主的文件，一般是垃圾文件

```
find / -nouser
```

-**type**

根据文件类型查找

```
-type d	目录
-type f	普通文件
-type l	软连接
```

-**a**

逻辑与

```
find . -size +1k -a -type f		查找当前目录下的大小超过1k的并且类型为文件的文件
```

-**o**

逻辑或

-**not**

逻辑非

```
find . -not -type d		查找当前目录下不是目录的文件
find . ! -type d		也可以加!
```

-**exec**

将前面找到的文件集合用后面的命令处理(固定格式)

```
find . -size +1k -a -type f -exec ls -lha {} \;
```

-**ok**

交互执行，会先询问

跟上面使用方式一致



#### gerp

Globally search a Regular Expression and Print

搜索文件中的数据(find是搜索文件名)

```
grep --color=auto hello abc		//在abc文件中查找hello字符串并高亮显示
```



|     选项     |    描述    |
| :----------: | :--------: |
|      -i      | 忽略大小写 |
| --color=auto | 找到的标红 |
|      -e      |  拓展正则  |
|      -v      |  结果取反  |
|              |            |

#### 通配符和正则表达式

find命令可以使用通配符进行模糊查询

grep命令可以使用正则表达式进行模糊查询

##### 通配符

​	用于匹配文件名，完全匹配

​	只要是操作文件名或是目录用，都可以使用通配符

```
例如 rm -rf /tmp/*		用到了通配符
```



| 通配符 |                             作用                             |
| :----: | :----------------------------------------------------------: |
|   ?    |                       匹配一个任意字符                       |
|   *    |      匹配0个或任意多个任意字符，也就是可以匹配任何内容       |
|   []   | 匹配括号中任意一个字符，例如[abc]代表一定匹配一个字符，a或者b或者c |
|  [-]   | 匹配括号中任意一个字符，-代表一个范围。[a-z]代表匹配一个小写字母 |
|  [^]   |          逻辑非，[ ^0-9 ]就是匹配一个不是数字的字符          |

```
find . -name "a?c"		匹配名字为a.c的(中间一个任意字符)
find . -name "ab*"		匹配名字开头为ab的文件名
find . -name "a[bc]c"	能匹配到abc和acc
find . -name "a[a-z]c"
find . -name "a[^b]c"	找中间字母不是b的文件名
```



##### 正则表达式

​	**包含匹配!!!!!!!!!!**

​	用于匹配文件中的数据，匹配字符串，包含匹配(非完全匹配)

| 正则符 |                    作用                    |
| :----: | :----------------------------------------: |
|   ?    |    匹配前一个字符重复0次或1次(扩展正则)    |
|   *    |        匹配前一个字符0次或任意多次         |
|   []   |          匹配中括号中任意一个字符          |
|  [-]   |             -代表一个范围[a-z]             |
|  [^]   | 逻辑非，[ ^0-9 ]就是匹配一个不是数字的字符 |
|   ^    |                  匹配行首                  |
|   $    |                  匹配行尾                  |

```
grep "a" abc			匹配有a的行，因为是包含匹配.如果是完全匹配的话，就是匹配这一行只有一个a的行
grep "a*" abc			匹配'a'0次或任意多次，意思就是全部都能匹配到
egrep "a?" abc			匹配所有
grep "aa*" abc			匹配至少有一个a的行(因为是包含匹配)
grep "^a" abc			匹配以a开头的
grep "[a-z]$" abc		必须是小写字母结尾
grep -v "[0-9]$" abc	不以数字结尾的行
```



#### 管道符

将命令1的结果交给命令2处理

想像一下:	将命令1的结果保存在了一个临时文件中，然后再用more查看文件

```
ll /etc/ | more		交由more处理ll找到的数据
find /etc/ | more	
```

#### netstat

|  选项   |               说明               |
| :-----: | :------------------------------: |
|   -a    | 列出所有网络状态，包括socket程序 |
|   -t    |          只显示TCP连接           |
|   -u    |          只显示UDP连接           |
|   -n    |         IP:端口格式显示          |
|   -l    |        只显示监听状态下的        |
|   -p    |         显示PID和程序名          |
|   -r    |            显示路由表            |
| -c 秒数 |     指定每隔几秒刷新网络状态     |

## 2020/1/14 9:30	双桥	BEGIN

#### alias

别名配置文件在家目录下的.bashrc里

```
alias grep='grep --color=auto'		后面必须加双引号
```

### 压缩命令

#### zip

​	为了和windows通用，不是Linux常见格式

​	-r	压缩目录

```
zip test.zip -r test/	把test目录压缩成test.zip
zip test.zip abc ABC	把abc和ABC文件压缩成test.zip
```



#### unzip

​	-d	指定压缩到哪里

```
unzip test.zip -d ./a		把test.zip压缩到当前目录的a目录里
unzip test.zip			    直接解压缩到当前目录下
```



#### gzip

​	不能打包

​	-r	压缩目录，不会归档，目录里面一个文件一个压缩包

​	默认不会保留源文件，而且也不用写压缩包的名字

```
gzip abc			压缩abc文件
gzip -d abc.gz		解压缩abc.gz压缩文件	解压缩会删除源压缩文件
gzip -c abc >> abc.gz		保留源文件
gzip -r a			把目录a下面的所有文件各自生成自己的压缩文件
```

#### gunzip

解压缩.gz压缩文件

```
gunzip abc.gz		解压缩abc.gz压缩文件
```



#### bzip2

​	不能压缩目录，直接报错

​	-d	解压缩

​	-k	压缩时，保留原文件

​	-v	显示详细信息

```
bzip2 abc		将abc文件压缩为abc.bz2压缩文件
bzip2 -d abc.bz2	解压缩
bzip2 -k abc	保留原文件
```

#### bunzip2

​	解压缩

#### tar	

​	打包命令

​	-c	打包

```
tar -cvf all.tar abc ABC abc_s 		打包abc，ABC，abc_s文件到all.tar，不会删除源文件的
tar -xvf all.tar					解打包
```



​	-f	指定压缩包的文件名

​	-v	过程

​	-x	解打包

​	**-z**	压缩或解压缩.tar.gz格式

```
tar -zcvf all.tar.gz abc ABC abc_s		压缩
tar -zxvf all.tar.gz					解压缩		解压缩不会删除源压缩文件
```



​	**-j**	压缩或解压缩.tar.bz2格式

```
tar -jcvf abc.tar.bz2 abc ABC abc_s 	压缩
tar -jxvf abc.tar.bz2 					解压缩		解压缩不会删除源压缩文件
```

​	-t	只查看

```
tar -ztvf all.tar.gz		只查看不解压
```



​	-C	解压缩到指定目录

```
tar -zxvf all.tar.gz -C /tmp/		解压缩并解打包到指定目录
```



```
tar -zxvf all.tar.gz -C /tmp/ abc		只解压压缩包中的abc文件到指定目录
```



### 关机和重启命令

#### sync

​	告知服务器将内存缓冲区的数据写入硬盘中，我即将要重启

#### shutdown

​	-c	取消关机或重启计划

​	-h	关机

​	-r 	重启

```
shutdown -h now		现在关机
shutdown -r now 	现在重启
shutdown -h 04:30	四点半关机
```



### 网络命令

#### 配置IP地址

##### 	setup(RedHat专有)

##### 	编辑配置文件

​		/etc/sysconfig/network-scripts/ifcfg-eth0



#### ifconfig

#### ping

​	-b	广播数据包，查看该网段有多少主机

​	-c	指定次数

#### netstat

​	详见上面

#### Linux的終端

tty0-tty6		6个本地终端		Alt+F1-F6切换

tty7			图形界面终端		Ctrl+Alt+F7	按住3s，并且安装了图形界面就可以打开

pts/0-255	远程终端			



#### Write

向同时在服务器上的终端发送信息

```
先敲一个w看看此时有多少用户正在登陆

[root@chuquwan ~]# w
 11:37:34 up 78 days, 22:34,  3 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty1                      05Nov19 69days  0.14s  0.14s -bash
root     pts/0    223.88.210.203   09:43    1:54m  0.00s  0.00s -bash
root     pts/1    223.88.210.203   11:33    6.00s  0.00s  0.00s w


write root tty1		按Ctrl+D发送
```

#### wall

给所有登陆用户发送信息，包括自己

```
wall "hello world"		向所有登陆用户发送信息
```

#### mail

发送邮件

-s	指定标题

```
mail sjh		给sjh用户发送邮件

mail			查看自己的邮件

mail -s "test mail" sjh < install.log	将install.log文件输入重定向到邮件内容并发送给sjh

```

每个用户的邮箱位置:	

```
/var/spool/mail/UserName
```



### 痕迹命令

#### w

当前正在登陆的用户

查看的是/var/run/utmp

```
w

 20:22:18 up  2:57,  4 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty1     -                17:25   17:52   0.06s  0.06s -bash
root     pts/0    192.168.2.175    20:04   17:12   0.10s  0.10s -bash
root     pts/1    192.168.2.175    20:05    0.00s  0.11s  0.01s w	//有w就代表是自己
root     pts/2    192.168.2.175    19:10   30:52   0.11s  0.00s -bash

```



#### who

跟w一样，只不过没有w详细

```
who

root     tty1         2020-01-14 17:25
root     pts/0        2020-01-14 20:04 (192.168.2.175)
root     pts/1        2020-01-14 20:05 (192.168.2.175)
root     pts/2        2020-01-14 19:10 (192.168.2.175)

```



#### last

查看的是/var/log/wtmp

查看系统所有登陆过的用户信息，包括正在登陆的用户和之前登陆的用户

```
//最上面是最新的
root     pts/1        192.168.2.175    Tue Jan 14 20:05   still logged in   
root     pts/0        192.168.2.175    Tue Jan 14 20:04   still logged in   
root     pts/2        192.168.2.175    Tue Jan 14 19:10   still logged in   
root     pts/2        192.168.2.175    Tue Jan 14 19:08 - 19:09  (00:01)    
root     pts/1        192.168.2.175    Tue Jan 14 17:44 - 20:02  (02:18)    
root     pts/0        192.168.43.123   Tue Jan 14 17:26 - 19:56  (02:30)    
root     tty1                          Tue Jan 14 17:25   still logged in   
reboot   system boot  2.6.32-754.el6.x Tue Jan 14 17:25 - 20:24  (02:59)    
root     pts/1        192.168.43.123   Tue Jan 14 05:17 - crash  (12:08)    
root     pts/0        192.168.43.123   Tue Jan 14 03:27 - crash  (13:58)    
root     tty1                          Tue Jan 14 03:23 - crash  (14:01)    
reboot   system boot  2.6.32-754.el6.x Tue Jan 14 03:20 - 20:24  (17:04)    
root     pts/0        10.34.16.216     Sun Dec 29 04:56 - down   (01:33)    
reboot   system boot  2.6.32-754.el6.x Sun Dec 29 04:32 - 06:29  (01:57)    
root     pts/0        10.34.16.216     Sat Dec 28 05:01 - down   (00:50)    
reboot   system boot  2.6.32-754.el6.x Sat Dec 28 05:00 - 05:52  (00:52)    
root     pts/1        10.34.16.216     Fri Dec 27 06:15 - down   (00:25)    
root     pts/0        10.34.16.216     Fri Dec 27 05:05 - down   (01:35)    
root     pts/0        10.34.16.216     Fri Dec 27 03:52 - 05:05  (01:13)    
root     pts/0        10.34.16.216     Fri Dec 27 01:53 - 03:52  (01:58)    
root     tty1                          Fri Dec 27 01:52 - down   (04:48)    
reboot   system boot  2.6.32-754.el6.x Fri Dec 27 01:52 - 06:40  (04:48)    
root     pts/0        192.168.43.123   Wed Dec 25 06:28 - 06:34  (00:05)    
root     tty1                          Wed Dec 25 06:18 - down   (00:16)    
reboot   system boot  2.6.32-754.el6.x Wed Dec 25 06:16 - 06:34  (00:18)    
root     tty1                          Wed Dec 25 06:04 - down   (00:11)    
reboot   system boot  2.6.32-754.el6.x Wed Dec 25 06:03 - 06:15  (00:12)    

wtmp begins Wed Dec 25 06:03:39 2019

```



#### lastlog

查看系统所有用户的登录状态(不管登陆了没有)

对应查看的文件是/var/log/lastlog

```
用户名           端口     来自             最后登陆时间
root             pts/4    192.168.43.123   二 1月 14 21:38:17 +0800 2020
bin                                        **从未登录过**
daemon                                     **从未登录过**
adm                                        **从未登录过**
lp                                         **从未登录过**
sync                                       **从未登录过**
shutdown                                   **从未登录过**
halt                                       **从未登录过**
mail                                       **从未登录过**
uucp                                       **从未登录过**
operator                                   **从未登录过**
games                                      **从未登录过**
gopher                                     **从未登录过**
ftp                                        **从未登录过**
nobody                                     **从未登录过**
dbus                                       **从未登录过**
rpc                                        **从未登录过**
vcsa                                       **从未登录过**
abrt                                       **从未登录过**
rpcuser                                    **从未登录过**
nfsnobody                                  **从未登录过**
haldaemon                                  **从未登录过**
ntp                                        **从未登录过**
saslauth                                   **从未登录过**
postfix                                    **从未登录过**
sshd                                       **从未登录过**
tcpdump                                    **从未登录过**
oprofile                                   **从未登录过**
sjh                                        **从未登录过**

```

#### lastb

查看错误登陆的信息

文件在/var/log/btmp

```

```

### 挂载命令

Linux系统会自动挂载非移动存储设备，但不会自动挂载移动存储设备（因为移动存储设备如果找不到的话系统无法正常启动）

Linux系统自动挂载的信息都在/etc/fstab文件中

```
fstab


#
# /etc/fstab
# Created by anaconda on Wed Dec 25 05:52:42 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#																	默认权限
UUID=9113c69a-d7f3-4efa-a651-0cc319572ae0 /                       ext4    defaults        1 1
UUID=4aa7bf08-6c47-4559-b9f0-5a186a763046 /boot                   ext4    defaults        1 2
UUID=9ff3b784-d835-40a9-a669-d9e1301828fb /home                   ext4    defaults        1 2
UUID=f3aad8aa-e336-4c75-8e90-9ce3f4566854 swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0

```



#### mount

```
[root@localhost ~]# mount
/dev/sda5 on / type ext4 (rw)		第一块硬盘的第5个分区挂载到了根目录下
proc on /proc type proc (rw)		内存挂载
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
tmpfs on /dev/shm type tmpfs (rw,rootcontext="system_u:object_r:tmpfs_t:s0")
/dev/sda1 on /boot type ext4 (rw)	第一块硬盘的第1个分区挂载到了/boot目录下
/dev/sda3 on /home type ext4 (rw)	第一块硬盘的第3个分区挂载到了/home目录下
none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)

```



```
mount -o remount,noexec	/boot	重新挂载boot分区，并使用noexec权限(boot分区里的可执行文件都无法执行报权限不足错误)
```



自己挂载：挂载到/mnt/...目录下

#### umount

卸载挂载的设备





#### **挂载光盘**

在/mnt目录下创建cdrom目录用于挂载

```
cd /mnt
mkdir cdrom
```

光盘的设备文件名默认为/dev/sr0

/dev/cdrom是/dev/sr0的软连接

如果有多个光盘数字往上加

```
mount -t iso9660 /dev/cdrom	/mnt/cdrom		挂载成功
```



#### 挂载U盘

##### fdisk命令

-l	列出所有硬盘分区信息

```
[root@localhost init.d]# fdisk -l

Disk /dev/sda: 21.5 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000eb6c5

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          26      204800   83  Linux
Partition 1 does not end on cylinder boundary.
/dev/sda2              26         287     2097152   82  Linux swap / Solaris
Partition 2 does not end on cylinder boundary.
/dev/sda3             287         542     2048000   83  Linux
Partition 3 does not end on cylinder boundary.
/dev/sda4             542        2611    16620544    5  Extended
/dev/sda5             542        2611    16619520   83  Linux

```



与mount的区别:

​	fdisk是查看所有硬盘的分区信息(当然也是可以查到未挂载的硬盘)，而且这个命令就不是查看挂载的命令

​	mount只能查看已挂载的分区信息



插入U盘，使用fdisk查看U盘的设备文件名(因为U盘和硬盘一起使用名称，所以每个人的系统中U盘的设备文件名不一定是相同的)

```
fdisk查看硬盘信息(主要找到设备文件名)

mount -t 文件系统类型	/dev/sdb1	/mnt/usb
mount -t ntfs -o iocharset=utf-8 /dev/sdb1	/mnt/usb	解决中文乱码

```



#### .ko结尾为驱动程序

#### .so结尾为库文件

#### 挂载NTFS文件系统

Linux默认不支持NTFS文件系统，因此需要安装驱动

驱动直接放入系统内核之中。这种驱动主要是系统启动加载必须的驱动，数量较少

驱动以模块的形式放入硬盘。大多数驱动都以这种方式保存，

保存位置在/lib/modules/2.6.32-754.el6.x86_64/kernel

驱动可以被Linux识别，但是系统认为这种驱动一般不常用，默认不加载，如果需要加载这种驱动，需要重新编译内核，而NTFS系统就是这种情况

硬件不能被Linux系统识别，需要手工安装驱动。这也是为什么第一次使用硬盘挂载Linux系统失败，因为系统不支持硬盘中的NTFS系统



## 2020/1/14	21:32	双桥END

## 2020/1/15	11:29	双桥BEGIN

### Linux软件包管理

Linux的RedHat系列的二进制包围.rpm类似于Windows下的.exe



#### rpm

rpm	-ivh

##### 安装

​	-i	install

```
rpm -ivh	包全名
```

​	-v	显示过程

​	-h	显示进度

```
[root@localhost Packages]# rpm -ivh mysql-connector-odbc-5.1.5r1144-7.el6.x86_64.rpm 
warning: mysql-connector-odbc-5.1.5r1144-7.el6.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID c105b9de: NOKEY
error: Failed dependencies:
	libodbcinst.so.2()(64bit) is needed by mysql-connector-odbc-5.1.5r1144-7.el6.x86_64
	unixODBC is needed by mysql-connector-odbc-5.1.5r1144-7.el6.x86_64

安装软件包出错，原因是依赖一个函数库文件不存在，那么怎么办呢？
肯定要安装这个函数库文件，但是这个函数库文件肯定在另一个软件包中，我又不知道那个软件包叫啥
登录www.rpmfind.net查询即可
```



​	--prefix	指定安装路径(一般不需要指定，**源码包需要指定，因为源码包没有RPM包的数据库信息以及系统服务启动脚本(/etc/init.d/)，删除和启动太麻烦了**)

​	--force	强制安装，不管有没有安装

##### 升级

​	-U		升级或者安装(如果没有则安装)

```
rpm -Uvh	包全名
```

​	-F		只升级

```
rpm -Fvh	包全名
```

##### 卸载

​	-e		卸载

```
rpm -e	包名即可
```

##### 查询

​	-q

```
rpm -q httpd		查询是否安装httpd

如果有内容就已安装
如果没有内容，就是没安装
```

​	-a	查询所有已安装的

```
rpm -qa

```

​	-i	information

```
rpm -qi	包名		查询已安装了的包信息，只需要包名
```

​	-p	package

```
rpm	-qip	包全名		可以查看未安装的RPM包的信息，必须是包全名
```

​	-l	list	列出软件包的所有安装列表以及软件所安装的目录

```
rpm -ql		包名		只需包名即可，查询软件包的文件列表
```

```
rpm -qlp	包全名		查询未安装的软件包的文件列表，必须是包全名
```

​	-f	查询系统文件属于哪个包

```
rpm -af	系统文件
```

##### 导入数字证书

作用:每次安装rpm包时检验其安全性

数字证书位置:

​	/etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

```
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6		

[root@localhost rpm-gpg]# rpm -qa | grep "gpg"
libgpg-error-1.7-4.el6.x86_64
gpgme-1.1.8-3.el6.x86_64
gpg-pubkey-c105b9de-4e0fd3a3		只要出现这个，就代表导入证书成功
pygpgme-0.1-18.20090824bzr68.el6.x86_64

```

##### 校验

-V

```
rpm -V	包名
```

-Vf

```
rpm -Vf	系统文件名
```

##### 提取RPM包中的文件

如果rpm安装的文件误操作了，那么如果调用rpm -ivh --force命令重新安装的话，误操作的文件内容还在，因为Linux是不会覆盖的。此时，如果想要解决这个问题，就应该只提取光盘中rpm二进制包中的某个文件

```
rpm2cpio /mnt/cdrom/Packages/httpd-2.2.15-69.el6.centos.x86_64.rpm | cpio -idv ./etc/httpd/conf/httpd.conf

将rpm二进制包中的文件提取到当前目录下的/etc/httpd/conf/httpd.conf文件中
```



##### RPM包命名规则

```
httpd-2.2.15-69.el6.centos.x86_64.rpm

httpd	软件包服务名称
2.2.15	版本号
69		发布的次数
el6		软件发型商
x86_64	硬件平台
```

包全名	安装未安装的包使用包全名，并且要指定绝对路径

包名	使用已安装的包直接使用包名即可，因为有一个数据库保存了已安装了的rpm包

​		在/var/lib/rpm



#### yum

yum源文位置:	/etc/yum.repos.d

yum源文件内容说明

```
[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

[base]	容器名称，一定要放在[]中
name	容器说明，可以随便写
mirrorlist	镜像站点
baseurl		镜像站点
enable		此 容器是否生效，不写或者写成enable=1表示此容器生效，enable=0代表不生效
gpgcheck	如果为1代表rpm的数字证书生效，就那个rpm --import -.-
gpgkey		数字证书的公钥文件保存位置
```

##### 搭建本地yum源

首先禁用掉主yum源，因为它要连网，而且国外服务器太慢，当然也可以连接中国的

```
怎么禁用呢？直接删掉肯定不行，改个名就可以。改了名不是.repo结尾，就不会生效了
mv CentOS-Base.repo CentOS-Base.repo.bak
```

搭建本地光盘yum源

```
编辑CentOS-Media.repo文件，照着下面改就可以了

[c6-media]
name=CentOS-$releasever - Media
baseurl=file:///mnt/cdrom	光盘挂载的位置
#        file:///media/cdrom/	
#        file:///media/cdrecorder/
gpgcheck=1
enabled=1	必须启用
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

```

##### list

```
yum list		列出服务器上所有可安装的软件包
```

##### search

```
yum search ifconfig		找出此命令所在的软件包
yun search 软件包部分包名	可以模糊查找软件包
```

##### install

```
yum -y install 包名		只需包名即可，不需要包全名，因为只有rpm手动安装才需要全部包名
```

##### whatprovides

```
yum whatprovides ifconfig	查询此命令来自那个包

[root@chuquwan7 ~]# yum whatprovides ifconfig
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
net-tools-2.0-0.25.20131004git.el7.x86_64 : Basic networking tools
源    ：installed
匹配来源：
文件名    ：/usr/sbin/ifconfig

```



##### update

```
yum -y update	包名			更新
yum	-y update				如果不加包名，那么就是更新本机所有的软件
```

##### remove

```
yum remove 	包名(谨慎卸载，依赖此包的包也会被卸载掉，将来再装此包只会装此包依赖的包，但不会装依赖此包的包)
```

##### grouplist

```
yum grouplist		列出可用的所有组包(包的集合)
```

##### groupsearch

```
yum groupsearch		搜索
```



##### groupinfo

```
yum groupinfo Eclipse	查询组包的信息
```



##### groupinstall

```
yum groupinstall Eclipse	安装
```

##### groupremove

```
yum groupremove Eclipse
```



#### 安装源码包

下载源码包

解压

```
tar -zxvf httpd-2.2.9.tar.gz
```

进入目录

```
cd httpd-2.2.9
```

##### 执行./configure

作用

​	检查系统环境

​	--prefix	指定安装路径，安装源码包需要指定安装路径

```
./configure --prefix /usr/local/apache2
```

##### 执行make

这个是编译命令，还不会往安装目录写文件

```
make
```



##### 执行make install

真正的往安装目录安装文件

```
make install
```

##### make clean 命令

如果中途出错，可以使用这个命令清理临时文件



##### 安装后如何启动

```
例如	httpd-2.2.9	会在安装目录下的bin目录下有一个apachectl文件
调用命令	apachectl start|stop
```



#### 脚本安装包

##### Xshell自带的上传文件功能

安装lrzsz

```
rpm -ivh lrzsz-0.12.20-27.1.el6.x86_64.rpm
```

```
[root@localhost Packages]# rz		这个是往Linux里传文件
```

```
[root@localhost Packages]# sz	文件名		上传文件
```

##### 安装

解压脚本安装包的压缩包

执行安装脚本

```
./setup.sh
```

OVER!

























### 启动系统服务命令

```
service network restart|start|stop
其实就是运行的/etc/rc.d/init.d/下的服务，也可以运行/etc/init.d/下的服务

init.d -> rc.d/init.d	init.d是rc.d/init.d的软连接
```



### httpd的文件位置

#### 网页文件位置

```
使用RPM包安装的网页文件位置在	/var/www/html
```

#### 配置文件位置

```
/etc/httpd/conf/httpd.conf
```







## 用户管理

#### 主要文件

##### 	/etc/passwd	

​		如果要将某一用户设置为管理员，只需将其UID置为0即可

```
sjh:x:500:500::/home/sjh:/bin/bash

第一列:	用户名
第二列:	密码位
第三列:	UID
第四列:	GID
第五列:	用户描述
第六列:	用户家目录
第七列:	用户可执行的权限
```



##### 	/etc/shadow

​	用户的密码信息文件

```
sjh:$6$ooVwyazV$0JUjvX6ejc0jKSwPwjo7aNpW8FC95NGlH/B8.WuPKVBAeqCuvHRs1YmjKNlVE9h63l4jKPEh9ELirHeDs/koR0:18275:0:99999:7:::

第一列:	用户名
第二列:	加密后的密码(sha512加密)
第三列:	最近一次修改密码时间距离1970-1-1的天数
第四列:	两次密码的修改间隔时间（不超过这个天数不允许改密码）
第五列:	密码有效期（改了密码会刷新）
第六列:	密码修改到期前的有效天数
第七列:	密码过期后的宽限天数
第八列：    密码失效时间(时间戳)
第九列:	保留
```



##### /etc/group

​	组信息文件

```
sjh:x:500:

第一列:	组名
第二列:	组密码位
第三列:	GID
第四列:	组中支持的其他用户，附加组是此组的用户
```

初始组：	每个用户只能属于一个初始组

附加组：	每个用可以加入多个附加组



##### /var/spool/mail

​	用户邮箱目录

##### /etc/skel

​	用户家目录文件的模板目录



#### 用户操作命令

##### 	useradd

​		添加新的用户

​			-u	指定UID

​			-g	指定初始组

​			-G	指定附加组

​			-c	说明

​			-d	目录

​			-s	shell



添加用户时参考两个配置文件

/etc/default/useradd

```
# useradd defaults file
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes


GROUP	
HOME		用户家目录的默认位置
INACTIVE	密码过期后的宽限天数，也就是/etc/shadow文件中的第七个字段
EXPIRE		密码的失效时间
SHELL		用户默认的shell
SKEL		
```



/etc/login.defs

```
MAIL_DIR	/var/spool/mail
//密码有效期时长
PASS_MAX_DAYS	99999		
//两次密码修改时间的最小间隔
PASS_MIN_DAYS	0
//密码最小长度
PASS_MIN_LEN	5
//密码过期前几天警告
PASS_WARN_AGE	7
UID_MIN                  1000
UID_MAX                 60000
SYS_UID_MIN               201
SYS_UID_MAX               999
GID_MIN                  1000
GID_MAX                 60000
SYS_GID_MIN               201
SYS_GID_MAX               999
CREATE_HOME	yes
UMASK           077
//删除用户是否连同组一起删除
USERGROUPS_ENAB yes
ENCRYPT_METHOD SHA512

```



##### 	





##### passwd

​			-l	锁住	相当于在/etc/shadow文件中的密码位前加了两个!

​			-u	解锁

​			--stdin	可以将管道输入的字符串作为密码

```
echo '123' | passwd --stdin user1		将user1的密码设置为123
```





##### chage

将/etc/shadow文件中的第三列(最近一次修改密码的时间戳)改为0，意思就是强制让用户一登陆就修改密码

```
chage -d 0 user1
```





##### userdel

​	删除用户

​		-r	删除用户的同时删除用户的家目录d

```
userdel -r user1
```



##### su

​	切换用户

​		-	切换成某一用户并且重新加载配置

​		-c	命令		仅执行一次命令，而不切换身份





#### 组操作命令

##### 	groupadd

​		添加组

##### 	geoupdel

​		删除组

##### 	gpasswd

​		操作组成员

​			-a		用户名:		把用户加入到组

​			-d		用户名:		把用户从组中删除

```
gpasswd -a user1 group1
gpasswd -d user1 group1
```

##### newgrp

```
newgrp g1		改变当前用户的有效组为g1，表示创建文件的所属组是g1
```





## 权限管理

#### ACL权限

单独给某一用户某一文件的某些权限

```
dumpe2fs -h /dev/vda1		检查跟分区是否有ACL权限


//出现以下内容，就是有ACL权限
Default mount options:    user_xattr acl
```

如果没有ACL权限的话

```
mount -o remount acl /		重新挂载
```

##### setfacl

​	-m		设置ACL权限

​	-R		递归设置ACL权限

​	-b		删除ACL权限

​	-x		删除指定用户的ACL权限

```
setfacl -m u:用户名:权限	文件名
setfacl -m g:组名:权限	文件名

setfacl -m u:st:5 /project/		对根下的project目录设置ACL权限

setfacl -m u:st:5	-R	/project/	对根下的project目录设置ACL权限并且递归

setfacl -m d:u:st:5	-R	/project	project目录以后生成文件都有此ACL权限

setfacl -m m:rx	/project/		设置此目录的最大有效权限mask为rx

setfacl -b /project/		删除所有的ACL权限

setfacl -x	u:st	/project/	删除st用户的ACL权限
```



##### getfacl

```
getfacl project/

# file: project/
# owner: sjh
# group: study
user::rwx
user:st:r-x
group::r-x
mask::r-x
other::---
```





#### sudo

赋予普通用户可以操作管理员命令的权限

##### visudo

```
这个是一个命令，编辑一下权限的内容
第一个ALL:此用户可以管理指定IP地址的主机
第二个ALL:可使用的身份，可以省略，省略就是所有可用的身份
第三个ALL:可以运行的命令

root    ALL=(ALL)       ALL
user1   ALL=/sbin/shutdown -r now
#如果是组的话，需要加%
%g1		ALL=

```



##### sudo

​	-l	列出当前用户所拥有的sudo权限



#### 特殊权限

##### setuid

​	4

​	必需加在可执行文件上

​	一旦可执行文件具有了SUID权限，那么该文件在运行时，就暂时拥有了该文件属主的权限

​	比如/usr/bin/passwd

```
-rwsr-xr-x 1 root root 27856 Aug  9 09:39 /usr/bin/passwd
具有SUID权限的程序，在属主权限那一栏中的执行权限x替换成了s（小）

find / -perm -4000		查询所有拥有suid权限的文件

chmod 5644	f	给f文件设置suid权限
```



##### setgid

​	对应组权限位置+s

​	2

​	可以加在文件或目录上

​	拥有gid权限的目录，在此目录下创建的文件或目录的属组都是此拥有gid目录的属组

##### stickybit

​	对应组权限位置+t

​	1

​	只能加载目录上

​	拥有此权限的目录，用户只能删除属于自己的文件

##### chattr

​	对文件赋予某些限制，root也被限制

​	+	赋予权限

​	-	去除权限

​	=

|      |                  文件                  |              目录              |
| :--: | :------------------------------------: | :----------------------------: |
|  i   | 不允许删除，改名，不允许添加或修改数据 |    不允许删除文件，新建文件    |
|  a   | 只能在文件中添加数据，不能删除修改数据 | 只允许建立修改文件，不允许删除 |

```
chattr +i test.txt
chattr -i test.txt
```



##### lsattr

​	-a	列出目录下的所有文件

​	-d	列出目录 



## 文件系统管理

#### df

​	只能查看已经挂载了的分区信息

​	统计空间的大小

​	查看硬盘各个分区的空间使用情况，包括临时文件等占用空间，所以有时候统计根的大小比du大

​	-a	显示所有分区包括交换分区

​	-h	人性化显示

​	-T	显示文件系统类型

```
[root@localhost ~]# df -a
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda5       16227248 2459656  12936616  16% /
proc                   0       0         0    - /proc
sysfs                  0       0         0    - /sys
devpts                 0       0         0    - /dev/pts
tmpfs             501508       0    501508   0% /dev/shm
/dev/sda1         194241   33998    150003  19% /boot
/dev/sda3        1983056    3024   1877632   1% /home
none                   0       0         0    - /proc/sys/fs/binfmt_misc

```

```
[root@localhost ~]# df -ah
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda5        16G  2.4G   13G  16% /
proc               0     0     0    - /proc
sysfs              0     0     0    - /sys
devpts             0     0     0    - /dev/pts
tmpfs           490M     0  490M   0% /dev/shm
/dev/sda1       190M   34M  147M  19% /boot
/dev/sda3       1.9G  3.0M  1.8G   1% /home
none               0     0     0    - /proc/sys/fs/binfmt_misc

```



#### du

​	统计文件的大小

​	-s	不显示子目录和子文件的占用量

​	-h	人性化显示

​	-a	显示每个子文件的磁盘占用量，默认只统计子目录的磁盘占用量

```
[root@localhost ~]# du -sh /
du: 无法访问"/proc/1986/task/1986/fd/4": 没有那个文件或目录
du: 无法访问"/proc/1986/task/1986/fdinfo/4": 没有那个文件或目录
du: 无法访问"/proc/1986/fd/4": 没有那个文件或目录
du: 无法访问"/proc/1986/fdinfo/4": 没有那个文件或目录
2.4G	/

```



#### dumpe2fs

​	显示磁盘状态

​	-h	只看超级块信息

```
[root@localhost ~]# dumpe2fs /dev/sda1 
dumpe2fs 1.41.12 (17-May-2010)
Filesystem volume name:   <none>
Last mounted on:          /boot
Filesystem UUID:          4aa7bf08-6c47-4559-b9f0-5a186a763046
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent flex_bg sparse_super huge_file uninit_bg dir_nlink extra_isize
Filesystem flags:         signed_directory_hash 
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              51200
Block count:              204800
Reserved block count:     10240
Free blocks:              160243
Free inodes:              51162
First block:              1
Block size:               1024
Fragment size:            1024
Reserved GDT blocks:      256
Blocks per group:         8192
Fragments per group:      8192
Inodes per group:         2048
Inode blocks per group:   256
Flex block group size:    16
Filesystem created:       Wed Dec 25 05:51:51 2019
Last mount time:          Tue Jan 21 11:38:24 2020
Last write time:          Tue Jan 21 11:38:24 2020
Mount count:              14
Maximum mount count:      -1
Last checked:             Wed Dec 25 05:51:51 2019
Check interval:           0 (<none>)
Lifetime writes:          43 MB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:	          128
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      3dd433dd-a148-4bc8-846c-48691929c86e
Journal backup:           inode blocks
Journal features:         (none)
日志大小:             4096k
Journal length:           4096
Journal sequence:         0x00000022
Journal start:            0


Group 0: (Blocks 1-8192) [ITABLE_ZEROED]
  校验和 0x33cb,2010个未使用的inode
  主 superblock at 1, Group descriptors at 2-2
  保留的GDT块位于 3-258
  Block bitmap at 259 (+258), Inode bitmap at 275 (+274)
  Inode表位于 291-546 (+290)
  3785 free blocks, 2010 free inodes, 6 directories, 2010个未使用的inodes
  可用块数: 4408-8192
  可用inode数: 39-2048

```



只看超级块信息

```
[root@localhost ~]# dumpe2fs -h /dev/sda1 
dumpe2fs 1.41.12 (17-May-2010)
Filesystem volume name:   <none>
Last mounted on:          /boot
Filesystem UUID:          4aa7bf08-6c47-4559-b9f0-5a186a763046
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent flex_bg sparse_super huge_file uninit_bg dir_nlink extra_isize
Filesystem flags:         signed_directory_hash 
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              51200
Block count:              204800
Reserved block count:     10240
Free blocks:              160243
Free inodes:              51162
First block:              1
Block size:               1024
Fragment size:            1024
Reserved GDT blocks:      256
Blocks per group:         8192
Fragments per group:      8192
Inodes per group:         2048
Inode blocks per group:   256
Flex block group size:    16
Filesystem created:       Wed Dec 25 05:51:51 2019
Last mount time:          Tue Jan 21 11:38:24 2020
Last write time:          Tue Jan 21 11:38:24 2020
Mount count:              14
Maximum mount count:      -1
Last checked:             Wed Dec 25 05:51:51 2019
Check interval:           0 (<none>)
Lifetime writes:          43 MB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:	          128
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      3dd433dd-a148-4bc8-846c-48691929c86e
Journal backup:           inode blocks
Journal features:         (none)
日志大小:             4096k
Journal length:           4096
Journal sequence:         0x00000022
Journal start:            0

```



#### fdisk

​	不支持GPT分区格式，只支持MBR

​	只能有四个主分区，逻辑分区编号从5开始，扩展分区不能写入数据，也不能格式化(添加文件系统)

​	给硬盘手动分区的命令

​	-l	查询硬盘分区状况

```
fdisk /dev/sdb		给sdb分区
n->p->1->+2G->
n->e->2->回车->回车->
n->l
```



#### mkfs

​	格式化硬盘分区，不能改变默认参数，比如块大小是4096字节

​	-t	指定文件系统

```
[root@localhost ~]# mkfs -t ext4 /dev/sdb1
mke2fs 1.41.12 (17-May-2010)
文件系统标签=
操作系统:Linux
块大小=4096 (log=2)
分块大小=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
131648 inodes, 526120 blocks
26306 blocks (5.00%) reserved for the super user
第一个数据块=0
Maximum filesystem blocks=541065216
17 block groups
32768 blocks per group, 32768 fragments per group
7744 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912

正在写入inode表: 完成                            
Creating journal (16384 blocks): 完成
Writing superblocks and filesystem accounting information: 完成

This filesystem will be automatically checked every 23 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
```



#### mke2fs

​	可以修改格式化分区的默认参数

​	-t	指定文件系统

​	-b	指定block块的大小

​	-i	指定多少字节分配一个inode

​	-j	建立带有ext3日志功能的文件系统

​	-L	给文件系统设置卷标

```
[root@localhost ~]# mke2fs -t ext4 -b 2048 /dev/sdb1
mke2fs 1.41.12 (17-May-2010)
文件系统标签=
操作系统:Linux
块大小=2048 (log=1)
分块大小=2048 (log=1)
Stride=0 blocks, Stripe width=0 blocks
131560 inodes, 1052240 blocks
52612 blocks (5.00%) reserved for the super user
第一个数据块=0
Maximum filesystem blocks=538968064
65 block groups
16384 blocks per group, 16384 fragments per group
2024 inodes per group
Superblock backups stored on blocks: 
	16384, 49152, 81920, 114688, 147456, 409600, 442368, 802816

正在写入inode表: 完成                            
Creating journal (32768 blocks): 完成
Writing superblocks and filesystem accounting information: 完成

This filesystem will be automatically checked every 32 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
```

#### 自动挂载

编辑/etc/fstab文件中的内容

```
[root@localhost disk1]# cat /etc/fstab 

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


第一列:UUID或者设备文件名都可以，推荐使用UUID
第二列:挂载点
第三列:文件系统类型
第四列:default就可以了
第五列:是否可以被备份	0=不备份，1=每天备份，2=不定期备份	推荐1
第六列:是否检测磁盘fsch	0=不检测，1=启动时检测，2=启动后检测	推荐2
```

分区的UUID在/dev/disk/by-uuid目录下

或者使用dumpe2fs命令查看



#### parted

​	支持GPT

​	-l	查看挂载的和未挂载的磁盘分区数据和文件系统类型

​		因为df -T只能查看已经挂载了的分区的文件系统类型，而fdisk -l不能查看文件系统类型，所以未挂载的分区的文件系统类型需要使用其他命令来查看

```
[root@localhost ~]# parted -l
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos	//代表是MBR分区表

Number  Start   End     Size    Type      File system     标志
 1      1049kB  211MB   210MB   primary   ext4            启动
 2      211MB   2358MB  2147MB  primary   linux-swap(v1)
 3      2358MB  4455MB  2097MB  primary   ext4
 4      4455MB  21.5GB  17.0GB  extended
 5      4456MB  21.5GB  17.0GB  logical   ext4

```



```
parted /dev/sdb
```

|  选项   |                        说明                        |
| :-----: | :------------------------------------------------: |
| mklabel |               改变此硬盘的分区表类型               |
| mkpart  |                    创建新的分区                    |
|   rm    |                    删除已有分区                    |
|  mkfs   |                格式化分区，只能ext2                |
|  print  |                    打印分区信息                    |
|    q    |                        退出                        |
| resize  | 调整分区大小，一定要先卸载分区，否则有可能丢失数据 |

```
(parted) mklabel gpt  


(parted) mkpart
分区名称？  []? disk1                                                     
文件系统类型？  [ext2]?                                                   
起始点？ 1MB                                                              
结束点？ 5gb


```



#### swap

##### free

​	显示当前系统内存的空间占用信息，**第二行才是真正的占用信息**

​	-h	人性化显示内存占用信息

```
[root@localhost ~]# free -h
             total       used       free     shared    buffers     cached
Mem:          979M       198M       781M       244K        11M        57M
-/+ buffers/cache:       128M       850M
Swap:         2.0G         0B       2.0G

buffers	向硬盘中写入数据的缓冲区
cached	读硬盘数据的缓存(如果有经常需要使用的数据，则放入cached里)

```



## 高级文件系统管理

#### 磁盘配额

是为了限制普通用户在分区上使用磁盘空间和文件个数的

空间大小限制和文件个数限制

空间大小限制：

​	软限制	超过此值会发出警告

​	硬限制	不能超过此值

首先查看系统是否支持磁盘配额

```
[root@localhost ~]# grep QUOTA /boot/config-2.6.32-754.el6.x86_64 
CONFIG_NETFILTER_XT_MATCH_QUOTA=m
CONFIG_XFS_QUOTA=y
CONFIG_QUOTA=y
CONFIG_QUOTA_NETLINK_INTERFACE=y
CONFIG_PRINT_QUOTA_WARNING=y
# CONFIG_QUOTA_DEBUG is not set
CONFIG_QUOTA_TREE=y
CONFIG_QUOTACTL=y


以上信息代表支持
```

查看是否有磁盘配额命令

```
[root@localhost ~]# rpm -qa | grep quota
quota-3.17-23.el6.x86_64

rpm -qa是查询所有已安装的软件包，查到了代表有
```



##### 开启挂载配额

​	只是临时生效

```
[root@localhost disk1]# mount -o remount,usrquota,grpquota /disk1/

[root@localhost disk1]# mount
/dev/sdb1 on /disk1 type ext4 (rw,usrquota,grpquota)
	
代表开启成功
```



​	永久生效，编辑/etc/fstab文件自动挂载

```
/dev/sdb1		/disk1		ext4	defaults,usrquota,grpquota	1 2	
```



##### 关闭SELinux

​	临时生效

```
[root@localhost disk1]# getenforce 	查看SELinux的开启信息，Enforcing意思为正在执行,enforce是执行
Enforcing

[root@localhost disk1]# setenforce 0	0是关，1是开
[root@localhost disk1]# getenforce 
Permissive		意思为宽容的意思

```

​	永久生效

​		修改/etc/selinux/config文件，需要重新启动

```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted 


enforcing	生效
permissive	宽容，会报警但不处理
disable		彻底残废
```



开始磁盘配额

##### quotacheck

```
quotacheck -avu	生成磁盘配额文件

[root@localhost disk1]# ls /disk1/
aquota.user  lost+found

已成功
```

如果要给根分区开启配额

除了要在/etc/fstab上修改信息外，还需执行以下命令

```
quotacheck -avum	m代表强制的意思
```



##### edquota

分配磁盘配额

-u	给用户分

-g	给组分

-t	设定宽限时间



```
[root@localhost disk1]# edquota -u user1

      1 Disk quotas for user user1 (uid 501):
      2   Filesystem                   blocks       soft       hard     inodes     soft     hard
       /dev/sdb1                         0          0          0          0        0          0                                           
       							已占用容量	软限制		硬限制		已占用文件数	软限制	硬限制
```



##### quotaon

启动磁盘配额

​	-a	依据/etc/mtab文件启动所有的配额分区，如果不加-a，就需要指定分区名

​	-u	启动用户配额

​	-g	启动组配额

​	-v	显示启动过程信息

```
[root@localhost disk1]# quotaon -uv /dev/sdb1 
/dev/sdb1 [/disk1]: user quotas turned on

```

##### quotaoff

关闭磁盘配额

​	-a

​	-u

​	-g

```

```



##### quota

查询用户或组配额信息

​	-u	查询用户配额

​	-g	查询组配额

​	-v	显示详细信息

​	-s	人性化显示容量大小

```
[root@localhost disk1]# quota -usv user1
Disk quotas for user user1 (uid 501): 
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
      /dev/sdb1       0   40000   50000               0       8      12        

```



##### repquota

查询分区配额信息

​	-a	显示所有

​	-v	显示详细信息

​	-u	只显示用户匹配

​	-s	人性化显示

```
[root@localhost ~]# repquota -avus
*** Report for user quotas on device /dev/sdb1
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      20       0       0              2     0     0       
user1     --       0   40000   50000              0     8    12       

Statistics:
Total blocks: 7
Data blocks: 1
Entries: 2
Used average: 2.000000

```



##### 测试

```
[user1@localhost disk1]$ dd if=/dev/zero of=/disk1/testfile bs=1M count=60
sdb1: warning, user block quota exceeded.
sdb1: write failed, user block limit reached.
dd: 正在写入"/disk1/testfile": 超出磁盘限额
记录了49+0 的读入
记录了48+0 的写出
51200000字节(51 MB)已复制，0.0369739 秒，1.4 GB/秒
```



##### setquota

​	自动配额

​	不需要手动使用VI输入

```
[root@localhost ~]# setquota -u user2 40000 50000 8 12 /disk1/

必须管理员执行
```





#### LVM

Logical volume Manager



物理卷(Physical Volume)

​	可以由一块硬盘上的不同分区组成，也可以由单独的一块硬盘组成

卷组(Volume Group)

​	一个或多个物理卷组成卷组

逻辑卷(Logical Volume)

​	相当于分区，可以格式化





我们需要准备三个分区，分区编号为8e

```
   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1               1        2189    17583111    5  Extended
/dev/sdb5               1         244     1959867   8e  Linux LVM
/dev/sdb6             245         488     1959898+  8e  Linux LVM
/dev/sdb7             489         732     1959898+  8e  Linux LVM

```



##### pv

###### pvcreate

​	创建物理卷

```
[root@localhost ~]# pvcreate /dev/sdb5
  Physical volume "/dev/sdb5" successfully created
[root@localhost ~]# pvcreate /dev/sdb6
  Physical volume "/dev/sdb6" successfully created
[root@localhost ~]# pvcreate /dev/sdb7
  Physical volume "/dev/sdb7" successfully created

```

###### pvscan

​	查询物理卷

```
[root@localhost ~]# pvscan
  PV /dev/sdb5                      lvm2 [1.87 GiB]
  PV /dev/sdb6                      lvm2 [1.87 GiB]
  PV /dev/sdb7                      lvm2 [1.87 GiB]
  Total: 3 [5.61 GiB] / in use: 0 [0   ] / in no VG: 3 [5.61 GiB]

```

###### pvdisplay

​	也是查询物理卷

```
[root@localhost ~]# pvdisplay 
  "/dev/sdb5" is a new physical volume of "1.87 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb5			pv名
  VG Name               					属于的vg名，由于还没有分配，所以空白
  PV Size               1.87 GiB			pv的大小
  Allocatable           NO					是否已经分配
  PE Size               0  					PE大小，由于还没有分配，所以还未指定 
  Total PE              0					PE总数
  Free PE               0					空闲PE数
  Allocated PE          0					可分配的PE数
  PV UUID               r9AMKp-FwrL-g73h-1zGP-MTS8-2SJn-woeviP		pv的UUID
   
  "/dev/sdb6" is a new physical volume of "1.87 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb6
  VG Name               
  PV Size               1.87 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               VxrcH6-Q8KJ-AjDZ-TSMo-bf1I-S55J-cefcVb
   
  "/dev/sdb7" is a new physical volume of "1.87 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb7
  VG Name               
  PV Size               1.87 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               rEDunL-0ex5-enaV-krJJ-ahyB-1Z7c-Lc38MV

```



###### pvremove

​	删除物理卷





##### vg

###### vgcreate

​	创建卷组

​	-s	指定PE大小默认为4MB

```
[root@localhost ~]# vgcreate sjhvg /dev/sdb5 /dev/sdb6 
  Volume group "sjhvg" successfully created

```



###### vgscan

```
[root@localhost ~]# vgscan
  Reading all physical volumes.  This may take a while...
  Found volume group "sjhvg" using metadata type lvm2

```



###### vgdisplay

```
[root@localhost ~]# vgdisplay 
  --- Volume group ---
  VG Name               sjhvg
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               3.73 GiB
  PE Size               4.00 MiB
  Total PE              956
  Alloc PE / Size       0 / 0   
  Free  PE / Size       956 / 3.73 GiB
  VG UUID               XY4wpr-4tTq-FcCU-UynM-POKR-WrYm-eXhNtC

```

###### vgextend

​	扩展卷组

```
[root@localhost ~]# vgextend sjhvg /dev/sdb7 
  Volume group "sjhvg" successfully extended

```



##### lv

###### lvcreate

​	在卷组中生成逻辑卷

​	-L	指定逻辑卷大小

​	-l	PE的个数

​	-n	逻辑卷名

```
[root@localhost ~]# lvcreate -L 3G -n lv1 sjhvg
  Logical volume "lv1" created.

创建逻辑卷成功，逻辑卷设备位置在/etc/sjhvg/lv1

lrwxrwxrwx 1 root root 7 1月  23 15:59 lv1 -> ../dm-0
```



###### lvscan

```
[root@localhost sjhvg]# lvscan 
  ACTIVE            '/dev/sjhvg/lv1' [3.00 GiB] inherit

```

###### lvdisplay

```
[root@localhost sjhvg]# lvdisplay 
  --- Logical volume ---
  LV Path                /dev/sjhvg/lv1
  LV Name                lv1
  VG Name                sjhvg
  LV UUID                zl2bk2-z8P5-JDra-GrGX-9JJ5-TU7F-TmOhZX
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2020-01-23 15:59:35 +0800
  LV Status              available
  # open                 0
  LV Size                3.00 GiB
  Current LE             768
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0

```



###### lvresize

​	调整逻辑卷大小，文件不会消失

​	-L	大小



```
[root@localhost sjhvg]# lvresize -L 4GB /dev/sjhvg/lv1 
  Size of logical volume sjhvg/lv1 changed from 3.00 GiB (768 extents) to 4.00 GiB (1024 extents).
  Logical volume lv1 successfully resized.

```



###### resize2fs

​	将扩展了的逻辑卷大小同步到系统中





2020/1/23	15:30	双桥END