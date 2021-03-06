# VI&VIM

## 2020/1/15 10:03	双桥BEGIN

插入命令

| 命令 |         作用         |
| :--: | :------------------: |
|  a   | 在光标坐在字符后插入 |
|  A   |  在光标所在行尾插入  |
|  i   | 在光标所在字符前插入 |
|  I   |  在光标所在行首插入  |
|  o   |   在光标下插入新行   |
|  O   |   在光标上插入新行   |

```
左下上右
HJKL

gg	移动到文首
G	移动到文尾
^	移动到行首
$	移动到行尾
:n	移动到第n行

x	删除当前光标所在的字母
nx	删除n个

dd	删除本行
ndd	删除n行
:n1,n2d	从n1行删到n2行
dG	从当前光标删除到文末尾
dgg	从当前光标删除到文件开头

yy	复制
nyy	复制多行

u	撤销
Ctrl+R	反撤销

r	只替换一个
R	一直往后替换


```



在家目录里编辑.vimrc文件(必须是隐藏文件)

```
:set nu			设置行号
:syntax	on/off	开启颜色，关闭颜色
:set hlsearch	开启搜索结果高亮显示
:set nohlsearch	不开启


```



#### 查找

```
/查找内容		光标所在行向下搜索
?查找内容		光标所在行向上搜索
n				下一个
N				上一个
```

#### 替换

```
:1,10s/old/new/g		1-10行内全局替换
:%s/old/new/g			替换整个文件
```

#### 导入文件内容

```
:r	/root/a/abc	将abc文件内容导入到当前光标的位置
```

#### 在VIM中使用系统命令

```
:!命令
```

#### 将命令的执行结果导入到光标所在行

```
:r !命令
```

#### 自定义快捷键

```
:map 快捷键 快捷键执行的命令	
:map	^p	I#<ESC>		按Ctrl+P时，在行首加入注释	
:map	^B	^x			按ctrl+B时，删除行首的第一个字母
```

^p代表Ctrl+p输入的时候是按Ctrl+v然后Ctrl+p，不能直接输入^p

**要想永久生效，写入家目录的.vimrc**

#### 字符替换

```
:ab	原字符	替换的字符
例如
:ab mymail 641756770@qq.com		以后再输入mymail按空格或者回车就变成了后面的
```

#### 多文件同时打开

```
vim -o abc ABC		上下分屏
vim -O abc ABC		左右分屏
Ctrl+w切换文件
```

## 2020/1/15	11:01	双桥END