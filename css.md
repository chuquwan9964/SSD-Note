# css笔记

#### css字体样式属性

##### css注释

```
/*	*/
```

##### font-size

设置字体大小

一般设置14px

##### font-family

设置字体

尽量写微软雅黑和宋体

|   字体   |       Unicode        |     英文名      |
| :------: | :------------------: | :-------------: |
| 微软雅黑 | \5FAE\8F6F\96C5\9ED1 | Microsoft YaHei |
|   宋体   |      \5B8B\4F53      |     SimSun      |

##### font-weight

设置字体粗细

|   值    |    描述    |
| :-----: | :--------: |
| normal  | 相当于400  |
|  bold   | 相当于700  |
| bolder  | 可能是900  |
| 100-900 |            |
| lighter | 更细的字体 |

##### font-style

设置字体是否倾斜

|   值   |  描述  |
| :----: | :----: |
| normal | 默认值 |
| italic |  倾斜  |

##### 字体综合设定

```
选择器{font: font-style font-weight font-size/line-height font-family;}

不需要设置的属性可以省略，但必须保留font-size和font-family，否则不起作用

p {
			font: 900 14px \5FAE\8F6F\96C5\9ED1;
			color: pink;
  }
```

#### 选择器

##### 标签选择器

```
标签名 {属性1:属性值1...}
```



##### 类选择器

```
.类名 {}
```



##### id选择器

```
#id {}
```



##### 通配符选择器

```
* {}
```

##### 子元素选择器

```
ul > li {
	color: red;
}
```

##### 后代选择器

```
ul li {}
```



##### 伪类选择器

###### 链接伪类选择器

**锚伪类**

```
a:link {color: #FF0000}		/* 未访问的链接 */
a:visited {color: #00FF00}	/* 已访问的链接 */
a:hover {color: #FF00FF}	/* 鼠标移动到链接上 */
a:active {color: #0000FF}	/* 选定的链接 */

在 CSS 定义中，a:hover 必须被置于 a:link 和 a:visited 之后，才是有效的
在 CSS 定义中，a:active 必须被置于 a:hover 之后，才是有效的
```



**伪类可以与 CSS 类配合使用**

```
a.red : visited {color: #FF0000}

<a class="red" href="css_syntax.asp">CSS Syntax</a>
```



###### 结构伪类选择器(css3的新特性)

**li:first-child**

**li:last-child**

**li:nth-child(n)**

​	**even**	偶数

​	**odd**		奇数

​	**n**

​	**2n**

**2n+1**

**li:nth-last-child(n)**	倒过来数

```
li:first-child {	//表示所有是孩子节点的li的第一个li,会选取下面的两个li
	color: red;
}
li:last-child {		//最后一个li节点
	color: blue;
}
li:nth-child(2) {
	color: red;
}

<body>
	<li>li</li>		//这个被选取
	<ul type="none">
		<li>1</li>	//这个被选取
		<li>2</li>
		<li>3</li>
		<li>4</li>
		<li>5</li>
		<li>6</li>
		<li>7</li>
	</ul>
</body>	


```

###### 目标伪类选择器

在a标签设置锚点时，谁被选中谁就变

```
:target {}
```



#### 颜色

RGB(red,green,blue)

#ffffff	纯白色	可以简写为	#fff	

#ffff0b	就不能简写了

```
p {
	color: rgb(255,255,255);
}

h2 {
	color: #fff;
}
```



#### 行间距line-height

```
p {
	line-height: 200px;
}
```

#### 水平对齐方式text-align

```
h2 {
	text-align: center;
}
```

#### 首行缩进text-indent

```
text-indent: 2em;	//两个字的宽度
```

#### 字间距letter-spacing

```
letter-spacing: 20px;
```

#### 单词间距(中文无效)word-spacing

```
word-spacing: 20px;
```

#### 颜色半透明

```
color: rgba(255,0,0,0.1);	a的取值范围是0-1之间，0代表全透明1代表不透明
```

#### text-shadow文字阴影

```
text-shadow: 水平位置 垂直位置 模糊距离 阴影颜色;

水平位置	负的往左，正的往右
垂直位置	负的往上，正的往下
```



#### css样式写在哪里

##### 1.行内样式表

##### 2.内部样式表

##### 3.外部样式表

```
<link rel="stylesheet" type="text/css" href="css文件的路径" />
```

#### 块级元素

```
常见的块级元素:	h1-h6,div,p		<div>最典型
```

**p标签和其他的文本块级元素标签内都不能防止块级元素**

```
比如我在p里放了一个块级元素

<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
		p {
			color: red;
		}
	</style>
</head>
<body>
	<p><div>hello world</div></p>
	<p>hello world</p>
</body>
</html>



```

在浏览器中解析成如下内容

![image-20200110140851962](F:\笔记\images\image-20200110140851962.png)



#### 行内元素(内联元素)

```
常见的行内元素:	span,a,strong,b,em,i,del,s,ins,u	<span>最典型
```

和相邻行内元素在一行上

高宽无效，但水平方向的padding和margin可以设置，垂直方向的无效

默认宽度就是它内容的宽度

行内元素只能容纳文本和其他的行内元素，不能容纳块级元素。（a特殊）



#### 行内块元素

在一行显示，但是可以改变宽高的元素

```
image,input,td都是行内块元素
```

