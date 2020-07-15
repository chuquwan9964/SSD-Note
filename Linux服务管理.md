

# Linux服务管理

### 6和7对比	

##### 文件系统

![58099133102](H:\笔记\images\1580991331025.png)

##### 服务启动

![58098486589](H:\笔记\images\1580984865892.png)



##### 网卡名称

![58099024231](H:\笔记\images\1580990242312.png)

##### 防火墙和数据库

![58099136975](H:\笔记\images\1580991369758.png)



![58099138915](H:\笔记\images\1580991389156.png)

##### 主机名

![58099141872](H:\笔记\images\1580991418720.png)

##### 7的网卡配置文件

![58099164661](H:\笔记\images\1580991646613.png)

##### 7修改网卡名

![58099211602](H:\笔记\images\1580992116023.png)



##### 常见端口

![58099382327](H:\笔记\images\1580993823271.png)



##### 网关和路由设置

![58099465303](H:\笔记\images\1580994653030.png)

#### 网络常见命令

##### 	

##### nslookup

​	dns测试命令

![58099520439](H:\笔记\images\1580995204394.png)

```
[root@localhost etc]# nslookup www.baidu.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
www.baidu.com	canonical name = www.a.shifen.com.
www.a.shifen.com	canonical name = www.wshifen.com.
Name:	www.wshifen.com
Address: 103.235.46.39

```



##### arp

​	将IP转为MAC地址

![58099631175](H:\笔记\images\1580996311756.png)

```
[root@localhost etc]# arp 192.168.43.123
Address                  HWtype  HWaddress           Flags Mask            Iface
192.168.43.123           ether   e4:70:b8:d4:30:8c   C                     eth0

```

##### nmap

​	网络探测扫描命令，可以攻击哟

​	默认未安装，直接yum -y install nmap

![58099651728](H:\笔记\images\1580996517289.png)

```
[root@localhost etc]# nmap -sP 192.168.43.0/24

Starting Nmap 5.51 ( http://nmap.org ) at 2020-02-06 06:29 CST
Nmap scan report for 192.168.43.1
Host is up (0.0050s latency).
MAC Address: 02:03:7F:95:BE:BE (Unknown)
Nmap scan report for 192.168.43.94
Host is up (0.00012s latency).
MAC Address: 00:0C:29:8E:31:37 (VMware)
Nmap scan report for 192.168.43.123
Host is up (0.00013s latency).
MAC Address: E4:70:B8:D4:30:8C (Unknown)
Nmap scan report for 192.168.43.175
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 30.62 seconds

```

```
[root@localhost etc]# nmap -sT 192.168.43.94

Starting Nmap 5.51 ( http://nmap.org ) at 2020-02-06 06:30 CST
Nmap scan report for 192.168.43.94
Host is up (0.98s latency).
Not shown: 999 filtered ports
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 00:0C:29:8E:31:37 (VMware)

```



### Services

#### SSL

​	OpenSSL

##### enc

​	加密命令

​	-a		进行64base编码

​	-in		指定文件

​	-out		指定加密后的文件

​	-salt	指定杂质

​	-e		加密

​	-d		解密

​	下图为enc所支持的加密算法

![58245350820](H:\笔记\images\1582453508200.png)

###### Cipher

​	加密文件

​	-e

```
[root@chuquwan6 cipher]# openssl enc -in fstab -out fstab.cipher -a -e 
```



###### DeCipher

​	解密文件

​	-d后面可以指定加密算法，这里未指定

```
[root@chuquwan6 cipher]# openssl enc -d -a -in fstab.cipher -out fstab
```



##### dgst

​	单向加密

​	消息摘要

​	取文件的特征码

```
#可以这样 实现
[root@chuquwan6 cipher]# md5sum fstab
68964488c4d0a3eae8daea060091f191  fstab

#可以使用dgst
[root@chuquwan6 cipher]# openssl dgst -md5 fstab
MD5(fstab)= 68964488c4d0a3eae8daea060091f191

```



##### rand

​	生成随机数，可以当密码

​	-base64		生成的随机数使用base64编码

​	-hex		生成的随机数表示为16进制形式

```
[root@chuquwan6 cipher]# openssl rand -base64 8
4SkfiB5kjFE=

[root@chuquwan6 cipher]# openssl rand -hex 8
6f95a5223db52ef4

```



##### genrsa

​	生成公钥对

![58246082593](H:\笔记\images\1582460825930.png)



##### CA

​	Certificate(证书) Authority(权威)

​	private CA

​	使用openssl生成私有CA的默认配置文件为/etc/pki/tls/openssl.cnf



​	**首先CA得有自己的证书才能给别人发证**

​		index.txt和serial文件都在/etc/pki/CA目录里

​		index.txt是证书的数据库

​		serial是发的序列号

![58285713573](H:\笔记\images\1582792628421.png)

```
[root@chuquwan6 CA]# openssl req -new -x509 -key /etc/pki/CA/private/cakey.pem -days 7300 -out /etc/pki/CA/cacert.pem
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:BeiJing
Locality Name (eg, city) [Default City]:BeiJing
Organization Name (eg, company) [Default Company Ltd]:chuquwan
Organizational Unit Name (eg, section) []:play
Common Name (eg, your name or your server's hostname) []:ca.chuquwan.com
Email Address []:caadmin@chuquwan.com

```





​	**给别人发证**

![58285973611](H:\笔记\images\1582859736118.png)





#### ssh

​	ssh是一个协议，secure shell

​	openssh是ssh协议的实现软件

​	两种连接方式，基于口令，基于秘钥



###### 基于口令

​	ssh root@IP

​	ssh -p 端口 用户名@IP

​	ssh	IP	不写用户名默认是root

###### 基于秘钥对

​	客户端先把自己的公钥放入服务器上

​	客户端发送连接请求，把自己的公钥发送给服务器

​	服务器判断此客户端发来的公钥和服务器上保存的公钥是否一样

​	如不一样，会送错误消息

​	如一样，服务器用此公钥加密一段验证信息，将验证信息和服务器的公钥发给客户端

​	客户端收到后，用自己的私钥解密，并用服务器的公钥加密，将加密后的信息发给服务器

​	服务器收到客户端发来的信息，用服务器的私钥解密此信息，判断和刚才发给客户端的信息是否一样

​	如一样，连接建立成功

​	不一样，建立失败



![58105502738](H:\笔记\images\1581055027381.png)



```
客户端生成的秘钥对默认保存在~/.ssh/id_rsa&~/.ssh/id_rsa.pub

客户端copy到服务器上的公钥默认保存在~/.ssh/authorized_keys	此文件和客户端公钥一致
```



```
windows使用xshell生成密钥对，将公钥内容复制到linux服务器的~/.ssh/authorized_keys文件中即可
```



**服务器的密钥对在/etc/ssh/目录下**

```
[root@chuquwan7 ~]# ls /etc/ssh/
moduli      sshd_config         ssh_host_ecdsa_key.pub  ssh_host_ed25519_key.pub  ssh_host_rsa_key.pub
ssh_config  ssh_host_ecdsa_key  ssh_host_ed25519_key    ssh_host_rsa_key

```

想使用密钥对方式登录哪个用户，就将公钥内容写入哪个用户~/.ssh/authorized_keys文件中(没有的话自己创建)



##### 配置文件

​	客户端配置文件/etc/ssh/**ssh_config**

​	服务器配置文件/etc/ssh/**sshd_config**

###### 禁用密码登录

​	**保障安全性**

```
PasswordAuthentication no	禁用密码登录
PasswordAuthentication yes	可以使用密码登录
```



​	编辑/etc/ssh/sshd_config配置文件，并修改其相关属性

![58105824807](H:\笔记\images\1581058248072.png)

改后重启sshd服务

```
service sshd restart
```

###### 禁止root远程登录

​	**保障安全性**

```
PermitRootLogin no
```

![58105835664](H:\笔记\images\1581058356644.png)



###### 更改端口

​	为了安全~~~

​	![58105913578](H:\笔记\images\1581059135788.png)



###### 限制ssh监听IP

​	为了安全，服务器上会有多块网卡也就是会有多个IP地址，我们不允许此服务器通过互联网登录，而想此服务器通过局域网登录，我们可以在机房中设置一台能够被外网远程连接的服务器，再通过此服务器连接那些只能局域网远程连接的服务器

​	将只能通过局域网连接的服务器监听IP做一下限制，让他只监听本机在局域网上的IP

![58105959542](H:\笔记\images\1581059595425.png)



##### 其他命令

###### scp

![58105968798](H:\笔记\images\1581059687984.png)



###### sftp

​	基于ssh的安全的文件传输协议，可以直接连接

![58106184718](H:\笔记\images\1581061847186.png)

```
sftp -oPort=6789 root@192.168.43.94		指定ssh服务的端口
```

![58106239293](H:\笔记\images\1581062392933.png)





#### TcpWrapper

​	**两个配置文件写完保存后就生效**

![58107784616](H:\笔记\images\1581077846162.png)



allow文件优先级大于deny文件

​	意思就是在allow文件里允许，deny文件里拒绝，那么还是允许

![58108014606](H:\笔记\images\1581080146065.png)

##### 配置文件编写规则

​	**/etc/hosts.allow**

​	**/etc/hosts.deny**

![58108056277](H:\笔记\images\1581080562773.png)



###### 拒绝某一主机

​	此配置文件写后保存既生效，不需重启

![58108116600](H:\笔记\images\1581081166006.png)



如果拒绝的主机为192.168.43.	那么就是拒绝此网段的主机



###### 拒绝所有

​	想要允许再去allow文件中添加即可，allow文件比deny文件优先级大

![58108135616](H:\笔记\images\1581081356168.png)









##### ldd

​	查看命令所依赖的库文件

![58108029516](H:\笔记\images\1581080295169.png)





#### DHCP

​	工作在局域网，因为只有局域网才需要分配IP地址，外网主机都有固定的外网地址

![58108205160](H:\笔记\images\1581082051607.png)



​	![58108228959](H:\笔记\images\1581082289593.png)



![58108658415](H:\笔记\images\1581086584150.png)





![58108645060](H:\笔记\images\1581086450607.png)



![58108655112](H:\笔记\images\1581086551125.png)





​			**2.5续约租约**

![58108662216](H:\笔记\images\1581086622160.png)



##### 环境搭建

​	关闭防火墙iptables

​	关闭selinux

​	设置为net

​	关闭虚拟机网络编辑器的自动dhcp功能

 ![58112770685](H:\笔记\images\1581127706853.png)



##### 配置文件详解

![58112983963](H:\笔记\images\1581129839632.png)



##### 修改配置文件

​	注意分号和大括号

![58113220629](H:\笔记\images\1581132206296.png)



**将其他subnet注释掉**

![58114016999](H:\笔记\images\1581140169992.png)





##### ifdown&ifup

​	指定某块网卡关闭或开启

​	在生产服务器，使用service network restart命令重启网络是重启所有网卡，生产服务器会有多张网卡，一下重启全部会造成网络不稳定



##### 地址保留	

​	将指定IP分配给固定的硬件地址

![58114051203](H:\笔记\images\1581140512030.png)



![58114170291](H:\笔记\images\1581141702914.png)





##### 超级作用域

​	一个c类网段地址不够用，需要进行单臂路由设置，但是我们虚拟机不能虚拟出一个路由，需要用虚拟机模拟路由转发，原理：

​		虚拟机网卡可以有多个网段，宿主机可以ping通这多个网段。有多个网段的虚拟机当做dhcp服务器，进行路由转发功能，就可以实现超级作用域

​	

###### 添加新的子网卡地址

```
[root@chuquwan7 network-scripts]# cp ifcfg-eth0 ifcfg-eth0:0
```

![58114337932](H:\笔记\images\1581143379326.png)



![58114342339](H:\笔记\images\1581143423392.png)



###### 开启路由转发

<https://blog.csdn.net/qianye_111/article/details/78987161>

```
[root@chuquwan7 etc]# vim /etc/sysctl.conf 
```

![58114407061](H:\笔记\images\1581144070611.png)



```
sysctl -p	加载一下
```



###### /etc/dhcp/dhcpd.conf

​	编辑之前，先把subnet全部删除

​	shared-network 后面的名字随便起

![58114546421](H:\笔记\images\1581145464218.png)



###### 快照克隆UUID重复问题

![58114863969](H:\笔记\images\1581148639697.png)





#### DNS

##### 	Resource Record

​	![58251271968](H:\笔记\images\1582512719683.png)

##### 	format

​		RR的书写形式

​		TTL:		服务器缓存此记录的时长，默认为秒

![58251298530](H:\笔记\images\1582512985307.png)

###### SOA

​	Start Of Authority

​	起始记录，定义一些相关配置信息

​	过期时间:		超过此时间仍无法从主服务器上更新到内容，则放弃更新

​	否定TTL:			无效解析记录的生存时间

![58251305682](H:\笔记\images\1582513056824.png) 

###### NS

​	Name Server

​	定义该区域的所有DNS服务器

![58251376303](H:\笔记\images\1582513763037.png)

###### MX

​	Mail Exchange

​	定义该区域的所有main服务器(smtp)

![58251378701](H:\笔记\images\1582513787010.png)



###### A

​	A记录,IPV4

![58251324969](H:\笔记\images\1582513249698.png)



###### AAAA

​	IPV6

![58251331240](H:\笔记\images\1582513312406.png)



###### PTR

​	IP	->	FQDN

![58251360136](H:\笔记\images\1582513601362.png)



###### CNAME

![58251370207](H:\笔记\images\1582513702072.png)





##### Conf

​	**配置文件**

​		/etc/named.conf					主配置文件

​		/etc/named.rfc1912.zones			区域辅助配置文件

​		/etc/rndc.key						用于本地连接的配置文件

​	**解析库文件**

​		/var/named/ZONE_NAME.ZONE

​	**注意**:

​		一个区域有一个区域库文件

​		一个DNS服务器可以为多个区域解析

​		必须有根区域文件

​		还应有实现localhost和本地回环地址的解析库文件



​	DNS服务的启动脚本为**/etc/init.d/named**



###### /etc/named.conf

​	主配置文件

​		**全局配置**

​			在option{}里定义

![58255017529](H:\笔记\images\1582550175299.png)



​		**日志子系统配置**

​			在logging{}中

![58255021696](H:\笔记\images\1582550216966.png)



​		**区域定义**

​			在zone{}中

​			zone "ZONE_NAME" IN{}

​			ZONE_NAME可以在解析库文件中使用@代替

![58255027167](H:\笔记\images\1582550271676.png)



##### Master Dns Server

​	主DNS服务器

###### 配置主配置文件

![58259980942](H:\笔记\images\1582599809420.png)



###### 添加区域库文件

​	在/etc/named.rfc1912.zones文件中添加区域定义

![58260102903](H:\笔记\images\1582601029037.png)



​	在/var/named目录中添加区域库文件

![58261118639](H:\笔记\images\1582611186394.png)



###### named-checkconf

​	检验named.rfc1912.zones配置文件是否有错误

###### named-checkzone

​	检验区域库文件的语法

```
[root@chuquwan6 named]# named-checkzone "chuquwan.com" /var/named/chuquwan.com.zone 
zone chuquwan.com/IN: loaded serial 2020022501
OK

```

###### 检测

​	先修改/etc/resolv.conf文件

![58261126886](H:\笔记\images\1582611268863.png)



​	nslookup命令测试

![58261132861](H:\笔记\images\1582611328611.png)



###### dig

​	测试DNS命令

![58261747774](H:\笔记\images\1582617477748.png)



```
dig -t A www.baidu.com @8.8.8.8
```

**dig的全量传送**

​	其实就是获得某一DNS服务器上的某一区域解析库文件的内容

​	相当危险的行为，我们将来配置DNS服务器时不应该让外人全量传送我们的服务器

```
dig -t axfr ZONE_NAME @SERVER

dig -t axfr "chuquwan.com" @192.168.2.131
```

![58261770140](H:\笔记\images\1582617701407.png)







##### Reverse Locale

​	反向区域设置

​	IP	->	FQDN

###### **添加反向区域**

![58261729740](H:\笔记\images\1582617297402.png)

###### **编写反向区域解析文件**

​	有NS,没有MX

​	NS用来找到从服务器通知他更新

![58261735580](H:\笔记\images\1582617355809.png)



###### **测试**

```
dig -x 192.168.2.131
```

![58261741889](H:\笔记\images\1582617418897.png)





##### Slave Dns Server

​	从DNS服务器

​	主DNS服务器的区域解析文件中必须有NS记录指向从服务器

​	从服务器只需定义区域，无需提供解析库文件，解析库文件在/var/named/slave/目录中（因为/var/named目录对于named用户没有写权限，所以从服务器需要一个拥有写权限的目录作为放置解析库文件的目录）



只需配置/etc/named.rfc1912.zones配置文件即可

![58262678208](H:\笔记\images\1582626782082.png)



反从就不写了，原理与上面一样





##### rndc

​	用于管理DNS服务的工具

​	Remote Name Domain Control

​	trace		日志级别

​	notify		手动提醒从服务器更新

![58262667337](H:\笔记\images\1582626673370.png)

​	



##### Sub Locale Authorize

​	子域授权

​	在com域内定义了自己的二级域的DNS服务器后，我们可以自定义子域，子域的DNS服务器必须在父域的解析库文件中有所登记



###### **1**

![58268415136](H:\笔记\images\1582684151369.png)



###### **2**

![58268420399](H:\笔记\images\1582684203991.png)

###### **3**

![58268427751](H:\笔记\images\1582684277511.png)









##### All Forward

​	全部转发服务器

​	对于forward的两种转发类型:

​		first:		如果转发服务器不吊我，我就自己找根

​		only:		如果转发服务器不吊我，我就放弃

![58263787492](H:\笔记\images\1582637874924.png)

##### Zone Forward

​	区域转发服务器

![58263791718](H:\笔记\images\1582637917183.png)





##### Safe

###### ACL

​	定义一个主机集合，集中控制，例如named.conf配置文件中的allow-query选项

​	可以用于对区域传送的访问控制(很重要吧)

​	定义在named.conf配置文件的option上面即可

![58268122214](H:\笔记\images\1582681222144.png)



###### Access Control Instruction

​	访问控制指令

​	既可以在主配置文件的option中全局定义.

​	也可以在区域配置文件中的区域中局部定义

![58268237006](H:\笔记\images\1582682370060.png)





##### View

​	称之为智能DNS

​	当我们需要根据客户端来源不同时，为其解析成不同的IP地址，我们就可以使用View

​	当使用view时，所有的zone都需要定义在view中

​	根zone只需定义在给需要递归的客户端所属的view中(一般来说，都是公司内部内网客户端，因为我们没有那么大的实力来给全网客户端进行递归)

​	不同的view可以有相同的域，不同的域解析库文件，用于给不同来源的客户端不同的答案

​	**Bind的View是CDN的一个实现，CDN也可以使用重定向来实现(那么此服务器就是负载均衡服务器)**

​	CDN:	Content Delivery Network	内容分发网络

```
用法:
	在/etc/named.rrf1912.zones配置文件中加入view
```





#### HTTP

​	IP地址分类

![58270610603](H:\笔记\images\1582706106030.png)



##### HTTP2.2

###### 	**<u>配置文件</u>**

​		/etc/httpd/conf/httpd.conf	

​		/etc/httpd/conf.d/*.conf

###### ​	**<u>服务脚本</u>**

​		/etc/rc.d/init.d/httpd

###### 	**<u>服务脚本配置文件</u>**

​		/etc/sysconfig/httpd

###### 	**<u>主程序文件</u>**

​		/usr/sbin/httpd(**default**)			基于prefork模型多进程(一个进程响应一个请求)

​		/usr/sbin/httpd.event				基于事件驱动模型(一个线程响应多个请求:IO复用	**想想netty**)

​		/usr/sbin/httpd.worker			基于多线程模型(多进程生成，一个进程生成多个线程，一个线程响应一个请求)

###### 	**<u>日志文件</u>**

​		/var/log/httpd

​			access_log:	访问日志

​			error_log:	错误日志

###### 	**<u>网页目录</u>**

​		/var/www/html



###### 	**<u>持久连接</u>**

​		HTTP1.1之后支持了持久连接

![58271933295](H:\笔记\images\1582719332950.png)

###### 	**<u>MPM</u>**

​		Multipath Process Module

​		多路处理模块，也就是prefork，worker，event三种模式 

​		使用**httpd -l**	查看当前模块

​		**httpd -M**查看当前装载的模块

![58272055605](H:\笔记\images\1582720556052.png)

​	

​	**编辑/etc/sysconfig/httpd**文件，修改其HTTPD属性，切换模块，并重启服务

![58278135177](H:\笔记\images\1582781351770.png)

![58272078566](H:\笔记\images\1582720785666.png)





###### 	**<u>ModelConf</u>**

​		定义prefork模式和worker模式的配置

​	**prefork**

![58272301544](H:\笔记\images\1582723015447.png)



​	**worker**

![58272308426](H:\笔记\images\1582723084264.png)





###### 	<u>**DSO**</u>

​		动态共享目录

​		更改/etc/httpd/conf/httpd.conf里面的module

![58272427773](H:\笔记\images\1582724277735.png)



###### 	**<u>Document Root</u>**

​		定义网页文件位置

![58272469943](H:\笔记\images\1582724699430.png)



###### 	**<u>Access Control</u>**

​	访问控制

​	**基于Location**

​		Location标签中写URL

​		在<Location></Location>标签中

​	**基于Directory**

​		Directory标签中写物理路径

​		在<Directory></Directory>标签中

​		**Options:**

​			Indexes:					是否索引文件，如果没有Index.html的话，会索引文件

​			FollowSymLinks:			是否跟随符号链接文件

![58276879624](H:\笔记\images\1582768796241.png)

​		

​		**AllowOverride:**

​			允许不允许各个网页路径下的.htaccess文件内容覆盖此配置文件

​			了解即可，一般为None，表示不让覆盖

![58276892204](H:\笔记\images\1582768922041.png)



​		**Order:**

​			allow deny		是白名单

​			deny allow		是黑名单

​		![58276895643](H:\笔记\images\1582768956437.png)

![58277307631](H:\笔记\images\1582773076316.png)



###### 	**<u>Directory Index</u>**

​		定义默认页面

![58276922609](H:\笔记\images\1582769226098.png)





###### 	**<u>Error Log</u>**

![58277000527](H:\笔记\images\1582770005277.png)



​	

###### 	**<u>Access Log</u>**

​		访问日志

![58277029788](H:\笔记\images\1582770297886.png)



​		**Log Format**

<http://httpd.apache.org/docs/2.2/mod/mod_log_config.html#formats>

​	![58277150892](H:\笔记\images\1582771508926.png)

![58277181080](H:\笔记\images\1582771810800.png)



![58277186810](H:\笔记\images\1582771868109.png)\



###### **<u>Alias</u>**

​	定义别名

​	格式

​		Alias	URL		/PATH/TO/SOMEFILE

![58277371929](H:\笔记\images\1582773719291.png)



###### **<u>Add Default Charset</u>**

​	服务器的默认字符集

​	此字符集表示发送的Response希望客户端浏览器使用什么字符集

![58277377088](H:\笔记\images\1582773770887.png)



###### **<u>Authentication By User</u>**

​	基于HTTP的username和password认证

​	**Basic**认证，网络上username和password明文传送

​		AuthType:		Basic和digest

​		AuthName:		随便给个名字(安全区的名字)

​		AuthUserFile:	账号密码文件所在路径

​		Require User:	允许登录的用户

![58277531724](H:\笔记\images\1582775317247.png)

![58277537296](H:\笔记\images\1582775372961.png)



​	![58277573768](H:\笔记\images\1582775737684.png)





###### **<u>Virtual Host</u>**

​	虚拟主机

​		怎么配置虚拟主机呢？

​			可以一台主机多个IP(IP太贵)

​			可以一台主机多个端口(端口别人不知道)

​			可以一台主机多个hostname(**常用**)

​	**如果要用虚拟主机，一定要将全局的DocumentRoot注释，以免出错**



**基于多IP**

![58278426518](H:\笔记\images\1582784265187.png)

**基于多HOSTNAM**

​	将下面的功能开启

![58278432696](H:\笔记\images\1582784326962.png)



###### **<u>Server-Status</u>**

​	可以在网页上查看本服务器的状态信息

![58278477418](H:\笔记\images\1582784774182.png)



##### CURL

​	curl命令

​	模拟各种TCP请求

​	curl - transfer a URL

![58279262842](H:\笔记\1582792628421.png)



##### DEFLATE

​	deflate

​	放气的意思(滑稽)

​	服务器压缩文件，提高效率

![58279428202](H:\笔记\images\1582794282023.png)



##### HTTPS

​	配置http服务器支持https

![58279595119](H:\笔记\images\1582795951190.png)



**可以适当的修改一下/etc/httpd/conf.d/ssl.conf配置文件**   

![58286395615](H:\笔记\images\1582863956150.png)



 当把CA机构给服务器的证书cp到服务器里，就可以启动HTTPS服务了





##### Pressure Test

​	apache的压力测试工具

![58286783289](H:\笔记\images\1582867832893.png)



```
 ab -c 100 -n 10000 http://192.168.2.140/messages
```

![58286933848](H:\笔记\images\1582869338484.png)





##### HTTP2.4

​	对比HTTP2.2:

​		<https://blog.51cto.com/leeyan/1696545>

​	在Centos6不可以直接安装，因为HTTPD2.4依赖的apr需要1.4版本，而在centos6上是1.3版本

​	所以先源码包安装apr和apr-util，再源码包安装httpd2.4

​	安装步骤:

​		<https://blog.csdn.net/qq_37187976/article/details/79210646>

​	**安装apr**

​		解压apr-1.6.5.tar.gz

​		进入

​		./configure --prefix=/usr/local/apr

​		make & make install

​	**安装apr-util**

​		解压

​		进入

​		./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr

​		make && make install

​	安装HTTP2.4

​		解压

​		进入

​		./configure --prefix=/usr/local/httpd24 --enable-so --enable-ssl --enable-cgi --enable-rewrite --with-zlib --with-pcre --with-apr=/usr/local/apr/ --with-apr-util=/usr/local/apr-util/ --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork

​		make && make install

​		



​		**一定要安装依赖包呀!!**







#### LAMP

​	**CGI**

​		Common Gateway Interface

​		公共网关接口

​		简单来说，就是前端代理服务器和后台程序的交接协议

​		那么前端httpd就相当于客户端，后台程序就相当于服务器

​		httpd -M | grep cgi

###### 	Centos7

​		httpd，php，php-mysql，mariadb-server

###### 	Centos6

​		httpd，php，php-mysql，mysql-server





#### MYSQL

​	Mariadb和Mysql

​	使用rpm包安装的mysql的

​	**数据位置在/var/lib/mysql**

​	**配置文件在/usr/share/mysql/*.cnf**

​	

​	运行mysql服务的配置文件查找顺序

​		/etc/my.cnf	->	/etc/mysql/my.cnf(这个目录不存在，需要手动创建)

​	

##### 	SQL

​		Structure Query Language

​		结构化查询语言

###### 	DDL

​		Data Definition Language

​		数据定义语言

​		**CREATE**

​		**DROP**

​		**ALTER**

###### 	DML

​		Data Manipulation Language

​		数据操纵语言

​		**SELECT**

​		**UPDATE**

​		**DELETE**

​	

###### 	数据库操作

![58295195083](H:\笔记\images\1582951950836.png)	



###### 	表操作

![58295205513](H:\笔记\images\1582952055133.png)



​	存储引擎:	一般使用InnoDB



​	**查看表的信息**

​		**HELP ALTER TABLE**

​		**HELP CREATE TABLE**

​		**授人以鱼不如授人以渔**

​		\G表示竖着显示

​		SHOW TABLE STATUS LIKE 'TABLE_NAME' \G

![58296048370](H:\笔记\images\1582960483702.png)



​	**添加枚举类型**

![58296129966](H:\笔记\images\1582961299663.png)

​	上个命令可以在后面加first和'COL_NAME' last表示将此列加入到表的第一列或指定列的后面





#### FTP

​	File Transform Protocol

​	默认监听在21号端口

​	

​	Server

​		vsftpd

​			Very Secure ......

##### vsftpd

​	主要配置文件

​	/etc/vsftpd/vsftpd.conf



###### 9-32

![58340450635](H:\笔记\images\1583404506350.png)



###### 93-101

![58340464785](H:\笔记\images\1583404647853.png)



###### 33-56

![58340507122](H:\笔记\images\1583405071227.png)



###### client restrictions

​	客户端限制选项

![58340694275](H:\笔记\images\1583406942756.png)



 