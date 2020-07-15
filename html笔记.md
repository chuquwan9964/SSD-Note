# 							MarkDown语法

**ctrl+b生成html的文件**

#	

##

###

####

#####

######

（-	列表）



**![图片名称](图片路径)**



**ctrl+T	表格**

**ctrl+I	斜体**



# 							html标签

### 排版标签

#### 标题标签

##### h1



##### h2



##### h3



##### h4



##### h5



##### h6



#### 段落标签

##### p	paragraph



#### 水平线标签

##### hr	horizental



#### 换行标签

##### br	break row



#### div

​	**division(分割，分区的意思)的缩写，是没有语义的**

#### span

​	**不会换行**

​	**没有语义**



#### 文本格式化标签

##### 加粗

###### b

###### strong

```
<b>加粗</b>
<strong>加粗</strong>
```

##### 斜体

###### i

###### em

```
<i>斜体</i>
<em>斜体</em>
```

##### 删除

###### s

###### del

```
<s>删除</s>
<del>删除</del>
```

##### 下划线

###### u

###### ins

```
<u>下划线</u>
<ins>下划线</ins>
```



##### img

**不会换行**

|   属性    |       值       |               描述               |
| :-------: | :------------: | :------------------------------: |
| src(必需) |      URL       |            图片的路径            |
|    alt    |     *text*     |        规定图像的替代文本        |
|  height   | *pixels*\| *%* |          设置图像的高度          |
|   width   |  *pixels\|%*   |          设置图像的宽度          |
|   title   |     *text*     |       鼠标悬停是现实的内容       |
|  border   |    *pixels*    | 不推荐使用。定义图像周围的边框。 |

##### a

**不会换行**

href:	*URL*	规定链接指向的页面的 URL

##### 锚点定位

```
<a href="#id"></a>	可以定位到指定id的地方
```



##### base

**定义在head里**

```
规定整体a标签的窗口打开方式
<base href="" target=""/>	//还能规定整体的url
```



#### 特殊字符的标签

```
空格	&nbsp;
<	&lt;
>	%gt;
'	&apos;
"	&quot;
¥	&yen;
®	&reg;
&	&amp;
```

#### 列表

##### 无序列表

```
<ul type="disc|square|circle|none">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
</ul>
```



##### 有序列表

```
<ol type="1|a|A|i|I">
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
</ol>
```





##### 自定义列表

```
<dl>
    <dt>计算机</dt>
    <dd>用来计算的仪器 ... ...</dd>
    <dt>显示器</dt>
    <dd>以视觉方式显示信息的装置 ... ...</dd>
</dl>
```

#### table

```
<table border="1" width="300" cellspacing="0" cellpadding="0">
	<caption>我的标题</caption>
	<tr>
	  <td>100</td>
	  <td>200</td>
	  <td>300</td>
	</tr>
	<tr>
	  <td>400</td>
	  <td>500</td>
	  <td>600</td>
	</tr>
</table>

cellspacing		单元格与单元格之间的距离
cellpadding		单元格与单元格里的内容之间的距离
```

#### form

##### input

```
男<input type="radio" name="gender" value="male" checked="true">
女<input type="radio" name="gender" value="female" >

checked="check|true"	都行

<input type="img" src="好看图片的路径"/>
```

##### label

**点击label中的内容就会获取焦点**

```
<label>用户名	<input type="text" name=""></label>
<label for="表单的id"></label>
```





# html5新增标签



##### header

**语义：定义页面的头部，页眉**

##### nav

**语义：定义导航栏**

##### footer

**语义：定义页面的底部**

##### article

**语义：定义文章**

##### section

**语义：定义区域**

##### aside

**语义：定义侧边**



##### datalist

**配合input标签使用**

```
<input type="text" name="" placeholder="请输入明星" list="star">
<datalist id="star">
    <option>张学友</option>
    <option>刘德华</option>
    <option>张国荣</option>
    <option>黄家驹</option>
</datalist>
```



##### fieldset

**配合legend一起使用**

```
	<fieldset>
		<legend>用户登录</legend>
		用户名<input type="text" name="">
	</fieldset>
```



#### 新增的input的type属性

| type  |       说明       |
| :---: | :--------------: |
| range |   自由拖动滑块   |
| email |                  |
|  url  |                  |
|  tel  |                  |
| time  | 上午\|下午时：分 |
| date  |      年月日      |
| mouth |       年月       |
| week  |       年周       |



#### input新增的属性

|     属性     |           描述           |                       用法                       |
| :----------: | :----------------------: | :----------------------------------------------: |
| placeholder  |      默认文字占位符      |                                                  |
|  autofocus   | 浏览器加载后自动获取焦点 |          <input type="text" autofocus>           |
|   multiple   |        多文件上传        |           <input type="file" multiple>           |
| autocomplete |    自动记录输入的信息    | <input type="text" autocomplete name="userName"> |
|   required   |     文本内容是否必需     |           <input type="text" required>           |
|  accesskey   |   按Ctrl+s定位到文本框   |        <input type="text" accesskey="s">         |

#### embed引入视频

```
<embed src=""></embed>
```

#### audio播放音频设备

```
<audio controls autoplay loop="-1">
	<source src="" type="">
	<source src="" type="">
</audio>

controls	显示控制组件
loop		循环播放的次数-1代表无限
autoplay	是否自动播放
```

