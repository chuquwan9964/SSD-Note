# CSS

#### 背景

##### background-image

```
background-image: url(images/H408.png);		指定图片位置
```

##### background-repeat

```
background-repeat: no-repeat;
					repeat-x	水平平铺，如果图片过小，横着来好几个图片
					repeat-y	竖直平铺
```

##### background-color

```
background-color: pink;		背景颜色
```

##### background-position

```
background-position: left top;		左上
background-position: left bottom;	左下
background-position: left center;	左中
background-position: center center;	水平是在中间，竖直也是在中间，也就是在盒子的正中间
background-position: left;			左中（如果其中一个不写，另外一个就是中）
background-position: bottom;		中下
background-position: 50% 50%		在正中间
```

##### background-attachment

背景附着，背景跟着滚轮一起走

```
background-attachment: fixed;		固定的，默认是scroll
```

##### 背景简写

```
background: 背景颜色 背景图片地址 背景平铺 背景滚动 背景位置

background: pink url(images/H408.png) no-repeat fixed center top;
```

##### 背景颜色半透明

```
background-color: rgba(255,0,0,0.9);	0代表全透明1代表不透明
```

##### 背景图片设置大小

```
background-size: 500px 500px;	可以设置背景图片的大小，设成和盒子一样大，你懂得

第一个是图片的宽度，第二个是图片的高度

如果不是设置背景图片的大小，而是Img标签的图片大小，直接使用height属性或width属性即可

background-size: 100%;	也可以这样

background-size: cover;	会自动调整缩放比例，保证图片始终填充满背景区域，如果有溢出则隐藏
						也就是拉住图片的右下角等比例往下拉，肯定不会拉的和盒子一样大，所以拉出去的部							分就会被隐藏
```



##### css层叠性

​	后面定义的属性会覆盖前面定义的属性

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div {
			color: red;
		}

		div {
			color: blue;	会覆盖
			font-size: 40px;
		}
	</style>
</head>
<body>
	<div>123</div>
</body>
</html>
```



##### css的继承性

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div {
			color: red;
			font-size: 30px;
		}

/*		p {
			color: blue;
			font-size: 50px;
		}*/
	</style>
</head>
<body>
	<div>
		<p>helo world</p> 如果p标签不指明属性的话，就会继承他爹的
	</div>
</body>
</html>
```



##### css的优先级

​	相同选择器按照长江后浪推前浪的原则，后面的覆盖前面的

​	**继承的权重为0**

|     选择器     |  权重   |
| :------------: | :-----: |
|   标签选择器   | 0,0,0,1 |
| 类选择器，伪类 | 0,0,1,0 |
|    ID选择器    | 0,1,0,0 |
|   !Important   | 无穷大  |

```
可以叠加

a:hover	的权重是0,0,1,1

<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div ul li {		权重为 0,0,0,3
			color: blue;
		}
		li {			权重为0,0,0,1
			color: red;
		}

	</style>
</head>
<body>
	<div>
		<ul>
			<li>123</li>
		</ul>
	</div>
</body>
</html>
```

!Important

```
	<!DOCTYPE html>
	<html>
	<head>
		<title></title>
		<style type="text/css">
			div ul li {
				color: blue;
			}
			li {
				color: red!important;
			}

		</style>
	</head>
	<body>
		<div>
			<ul>
				<li>123</li>
			</ul>
		</div>
	</body>
	</html>
```



#### 盒子

##### 	padding

​		填充的意思

​		盒子内边距，也就是盒子中的内容和盒子的边框之间的距离，原本距离为0

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div {
			width: 100px;
			height: 100px;
			border: 1px solid red;
			padding: 10px 10px 10px 10px;	上 右 下 左
		}
	</style>
</head>
<body>
	<div>hello world</div>
</body>
</html>
```

![58036385960](H:\笔记\images\1580363859604.png)

```
上面的图，盒子大小还是100px，只不过盒子内容和边框之间的距离为10px，边框宽度为1px
```



##### 	margin

​		余量的意思

​		盒子外边距，盒子边框之间的距离

​		这个是定义盒子与盒子边框之间的距离

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div:first-child {
			width: 200px;
			height: 200px;
			margin: 20px;
			background-color: red;
		}
		div:last-child {
			width: 200px;
			height: 200px;
			margin: 40px;
			background-color: green;
		}
	</style>
</head>
<body>
	<div></div>
	<div></div>
</body>
</html>
```

![58036972151](H:\笔记\images\1580369721514.png)

```
上图中，上面红色底部边框距离绿色顶部边框距离为40px，我想说明什么呢？margin挺简单的
```



##### margin水平居中

margin:0 auto;

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div {
			height: 300px;
			width: 300px;
			background-color: red;
		}
		div div {
			height: 100px;
			width: 50px;
			background-color: green;
			margin: auto auto;
		}
	</style>
</head>
<body>
	<div>
		<div></div>
	</div>
</body>
</html>
```

![58037023935](H:\笔记\images\1580370239356.png)

margin总结

```
尽量不要给行内块元素设置margin的上下边距，可以设置左右
margin就是边框与边框之间的距离
```





##### border-width

​	定义边框的宽度

```
border-width: 3px;
```



##### border-color

​	定义边框的颜色

```
border-color: green;
```



##### border-style

​	定义边框的样式

```
none		无
solid		细线
dashed		虚线
dotted		点
double		双实线
```

##### border-top-color

##### border-top-style

##### border-top-width



##### 边框综合设定

```
border-top: 1px solid red	宽度 样式 颜色
```



##### 合并边框border-collapse

​	做一个表格，table有边框1px，td有边框1px，如果设置cellspacing=0和cellpadding=0，那么table的边框将会和td的边框合并，成为2px，那么如果解决这个问题？

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		table {
			width: 200px;
			height: 200px;
			border: 1px solid pink;
			border-collapse: collapse;
		}

		td {
			border: 1px solid pink;
			text-align: center;
		}
	</style>
</head>
<body>
	<table cellspacing="0" cellpadding="0">
		<tr>
			<td>12</td>
			<td>12</td>
			<td>12</td>
			<td>12</td>
		</tr>
		<tr>
			<td>12</td>
			<td>12</td>
			<td>12</td>
			<td>12</td>
		</tr>
		<tr>
			<td>12</td>
			<td>12</td>
			<td>12</td>
			<td>12</td>
		</tr>
		<tr>
			<td>12</td>
			<td>12</td>
			<td>12</td>
			<td>12</td>
		</tr>
		<tr>
			<td>12</td>
			<td>12</td>
			<td>12</td>
			<td>12</td>
		</tr>
	</table>
</body>
</html>
```



##### 圆角矩形border-radius

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div {
			width: 200px;
			height: 200px;
			background-color: pink;
			border: 1px solid red;
		}

		div:first-child {
			border-radius: 10px;
		}
	</style>
</head>
<body>
	<div></div>
	<div></div>
	<div></div>
	<div></div>
	<div></div>
</body>
</html>
```

```
border-radius: 10px 20px 30px 40px;
```



#### 新浪导航栏

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		nav {
			height: 50px;
			background-color: white;
			border-top: 2px double orange;
			border-bottom: 2px solid #EDEEF0;
		}	
		a {
			text-align: center;
			padding: 15px 40px;
			width: 100px;
			height: 20px;
			line-height: 20px;		/*行高设置为和高度相等文字可以垂直居中*/
			font-size: 14px;
			color: #4c4c4c;
			text-decoration: none;	/*超链接无下划线*/
			display: inline-block;	/*使a标签成为行内块元素,a本来是行内元素*/
		}

		a:hover {
			background-color: #cccccc;
		}
	</style>
</head>
<body>
	<nav class="topbar">
		<a href="#">设为首页</a>
		<a href="#">手机新浪网</a>
		<a href="#">移动客户端</a>
		<a href="#">博客</a>
		<a href="#">微博</a>
		<a href="#">关注我</a>
	</nav>
</body>
</html>
```



##### 案例总结

```
padding算是在内容的一部分
比如一个div宽200px，高200px，设置padding全部都是15px，那么这个div如果加背景色的话，背景色的宽高是215px

margin不算内容的一部分

display可以改变标签的显示style
```



##### 外边距合并

​	如果子盒子和父盒子左上角重叠，就会发生外边距合并，导致子盒子会带着父盒子跑

​	解决方法如下

###### 	overflow

```
	<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		* {
			margin: 0;
			padding: 0;
		}
		.out {
			height: 300px;
			width: 300px;
			background-color: red;
			overflow: hidden;	或者设置一个边框，设置padding为1px也行
		}
		.inner {
			height: 50px;
			width: 50px;
			background-color: green;
			margin: 125px auto;
		}
	</style>
</head>
<body>
	<div class="out">
		<div class="inner"></div>
	</div>
</body>
</html>
```



##### padding不增大盒子

​	如果该盒子没有直接指定宽度，那么padding对于左右就不会撑大此盒子，高度也一样



##### 盒子阴影

​	**box-shadow**

```
box-shadow:	水平位置 垂直位置 模糊距离 阴影大小  阴影颜色 内/外阴影
```



​	前两个属性必须写，outset(外阴影)是默认的，不用写，如果写了就报错

​	内阴影是inset

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div {
			margin: 200px;
			width: 100px;
			height: 300px;
			border: 1px solid #ccc;
		}

		div:hover {
			margin-top: 197px;
			box-shadow: 0 15px 30px rgba(12,12,12,0.3);
		}
	</style>
</head>
<body>
	<div></div>
</body>
</html>
```



#### 浮动

​	早期用来做文字环绕图片用的，现在多用于布局

​	将标准流转换为浮动流

​	可以实现多个块元素在一行内布局，虽然display:inline-block也可以做到，但是后者显然会有些瑕疵

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		* {
			padding: 0;
			margin: 0;
		}
		div:first-child {
			width: 100px;
			height: 100px;
			background-color: pink;
			float: right;
		}
		div:nth-child(2) {
			width: 100px;
			height: 100px;
			background-color: hotpink;
			float: right;
		}
	</style>
</head>
<body>
	<div></div>
	<div></div>
</body>
</html>
```

![58045701604](H:\笔记\images\1580457016047.png)



```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		* {
			padding: 0;
			margin: 0;
		}
		div:first-child {
			width: 100px;
			height: 100px;
			background-color: pink;
			float: left;
		}
		div:nth-child(2) {
			width: 100px;
			height: 100px;
			background-color: hotpink;
			/*float: left;*/	因为上面是left浮动，相当于在天上，此div在浮动div的下面，被覆盖
		}
	</style>
</head>
<body>
	<div></div>
	<div></div>
</body>
</html>
```

![58045709915](H:\笔记\images\1580457099156.png)



##### 浮动必须有父元素

​	浮动的子元素外部必须有父元素，且父元素必须是标准流元素，也就是不是浮动，占据着主阵地

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div .son {
			width: 200px;
			height: 200px;
			background-color: red;
			float: left;
		}
		.outer {
			width: 300px;
			height: 300px;
			background-color: orange;
		}
	</style>
</head>
<body>
	<div class="parent" style="height: 200px">	父元素规定了高度和子元素一样高
		<div class="son"></div>
	</div>
	<div class="outer"></div>
</body>
</html>
```



##### 浮动特性

​	子元素浮动顶多到父元素的内容与边框的分界线

​	也就是说父元素如果是宽200px，高400px，那么子元素浮动的话就只能在200x400里浮动，父元素加了padding或者加粗border也不会增大子元素的浮动范围



​	浮动了的元素都是行内块元素

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		div {
			height: 200px;
			background-color: red;
			float: left;
		}

		span {
			height: 100px;
			background-color: orange;
			float: left;	如果去了浮动，span不会在红色的下面，还是在下图的那个位置，只不过高度无效(因为是行内元素，高度无效)
		}
	</style>
</head>
<body>
	<div>123456</div>
	<span>12345678</span>
</body>
</html>

```

![58054416947](H:\笔记\images\1580544169471.png)



#### 版心

##### 	一列固定宽度且居中

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		* {
			margin: 0;
			padding: 0;
		}
		.top,
		.banner,
		.main,
		.footer {
			width: 960px;
			margin: 10px auto;
			text-align: center;
		}
		.top {
			height: 80px;
			background-color: pink;
		}
		.banner {
			height: 120px;
			background-color: purple;
		}

		.main {
			height: 500px;
			background-color: hotpink;
		}
		.footer {
			height: 150px;
			background-color: gray;
		}
	</style>
</head>
<body>
	<div class="top">top</div>
	<div class="banner">banner</div>
	<div class="main">main</div>
	<div class="footer">footer</div>
</body>
</html>
```

![58054556202](H:\笔记\images\1580545562022.png)



##### 两列左窄右宽

###### 	list-style: none;

​	去掉列表的点

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		* {
			margin: 0;
			padding: 0;
		}
		.top,
		.banner,
		.main,
		.footer {
			width: 960px;
			text-align: center;
			margin: 10px auto;
		}

		.top {
			height: 80px;
			background-color: pink;
			margin-top: 0;
		}

		.banner {
			height: 40px;
			background-color: hotpink;
		}

		.main {
			height: 400px;
		}

		.left {
			float: left;
			height: 400px;
			width: 300px;
			background-color: gray;
		}

		.right {
			width: 640px;
			height: 400px;
			background-color: #ccc;
			float: right;
		}
	</style>
</head>
<body>
	<div class="top">top</div>
	<div class="banner">banner</div>
	<div class="main">
		<div class="left">left</div>
		<div class="right">right</div>
	</div>
	<div class="footer">footer</div>
</body>
</html>
```

![58054753544](H:\笔记\images\1580547535448.png)



##### 通栏平均分布型

​	锤子官网

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		* {
			padding: 0;
			margin: 0;
		}
		.top {
			height: 45px;
			background-color: black;
			color: white;
			text-align: center;
			line-height: 45px;
		}

		.banner,
		.main,
		.main1,
		.main2 {
			width: 990px;
			text-align: center;
			margin: 10px auto;
		}

		.banner {
			height: 100px;
			background-color: gray;
		}

		.main1 {
			height: 100px;
			background-color: red;
		}
		.main1 div {
			margin-right: 10px;
			height: 100px;
			width: 240px;
			float: left;
			background-color: #ccc;
		}
		.main1 div:last-child {
			margin-right: 0;
		}
		.main2 {
			height: 200px;
			background-color: pink;
		}
	</style>
</head>
<body>
	<div class="top">top</div>
	<div class="banner">banner</div>
	<div class="main">
		<div class="main1">
			<div></div>
			<div></div>
			<div></div>
			<div></div>
		</div>
		<div class="main2">
			<div></div>
			<div></div>
			<div></div>
			<div></div>
		</div>
	</div>
</body>
</html>
```

![58055160004](H:\笔记\images\1580551600044.png)



#### 清除浮动

​	子元素为浮动后，导致父元素的高度为0，就会被其他的盒子占领

​	**浮动元素不会继承父元素的宽度属性，父元素的宽高也不会因为浮动元素而改变**

##### 额外标签法

​	**不推荐使用**,会使代码多很多无用标签

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		.father {
			background-color: red;
		}

		.son1 {
			height: 200px;
			width: 300px;
			background-color: orange;
			float: left;
		}

		.son2 {
			height: 100px;
			width: 300px;
			background-color: pink;
			float: left;
		}

		.out {
			height: 200px;
			background-color: black;
		}
	</style>
</head>
<body>
	<div class="father">
		<div class="son1"></div>
		<div class="son2"></div>
		<div style="clear: both;"></div>	使用额外标签
	</div>

	<div class="out"></div>
</body>
</html>
```

![58055713718](H:\笔记\images\1580557137185.png)



##### 父元素添加overflow

​	**overflow: hidden**

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		.father {
			background-color: red;
			overflow: hidden;		父级元素添加overflow=hidden，会触发BFC
		}

		.son1 {
			height: 200px;
			width: 300px;
			background-color: orange;
			float: left;
		}

		.son2 {
			height: 100px;
			width: 300px;
			background-color: pink;
			float: left;
		}

		.out {
			height: 200px;
			background-color: black;
		}
	</style>
</head>
<body>
	<div class="father">
		<div class="son1"></div>
		<div class="son2"></div>
	</div>

	<div class="out"></div>
</body>
</html>
```



##### 双伪元素

```
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		.father {
			background-color: red;
		}

		.son1 {
			height: 200px;
			width: 300px;
			background-color: orange;
			float: left;
		}

		.son2 {
			height: 100px;
			width: 300px;
			background-color: pink;
			float: left;
		}
		.out {
			height: 200px;
			background-color: black;
		}

		.clearfix:before,
		.clearfix:after {
			content: "";
			display: table;
		}
		.clearfix:after {
			clear: both;
		}
		.clearfix {
			*zoom: 1;	/*为了兼容低版本的浏览器*/
		}
	</style>
</head>
<body>
	<div class="father clearfix">
		<div class="son1"></div>
		<div class="son2"></div>
	</div>

	<div class="out"></div>
</body>
</html>
```



![58055847386](H:\笔记\images\1580558473867.png)