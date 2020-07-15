# PHP

php的代码写在<?php?>里



#### 定义变量

```
$a = 10;	定义变量a的值为10

$b = $a;	定义变量b的值为变量a的值，a改变b不会改变

$c = &$a;	使c的地址和a的地址一样，这样a改变c也会改变

$arr = array(1,2,3,4,5);	定义数组，可以容纳所有类型的数据	array是个函数
print_r($arr);		打印数组
```



```
<?php
	$arr = array(1,2,3,4,5);
	echo "<pre>";
	print_r($arr);
	echo "</pre>";

	$a = "hello world";//也可以'hello world'
	echo $a;
?>
```



#### 字符串连接符

```
java中的是+
php中的是.


<?php
	$a = 'wh';
	$b = 'I love '.$a.' and wh love me too';
	echo $b;
?>

I love wh and wh love me too
```



#### 字符串单双引号

##### 	单引号

```
单引号不能解析变量

<?php
	$a = 'wh';
	$b = 'I love '.$a.' and wh love me too';
	$c = 'I love $a and $a love me too';
	echo $c;
?>

I love $a and $a love me too

```

##### 	双引号

```
双引号可以解析变量

<?php
	$a = 'wh';
	$b = 'I love '.$a.' and wh love me too';
	$c = "I love $a and $a love me too";
	echo $c;
?>

I love wh and wh love me too


如果使用双引号解析变量，请这样书写
不加大括号，也就是说$a也可以，只不过如果打印$ab就不好了

<?php
	$a = 'wh';
	$c = "I love {$a} and {$a} love me too";
	echo $c;
?>


```



**双引号不能解析表达式，例如**

```
<?php
	$a = 10;
	$b = 20;
	echo "the number is {$a}+{$b}";
?>

请这样书写

<?php
	$a = 10;
	$b = 20;
	echo "the number is ".($a+$b);
?>
```



#### 函数

##### gettype

​	获取变量类型

```
<?php
	$a = 10;
	$b = null;
	$c = 'wh';

	echo gettype($a);	//integer
	echo gettype($b);	//NULL
	echo gettype($c);	//string
	echo gettype($d);	//NULL	调用gettype传入一个没有定义的值，得到NULL
	
?>
```



##### var_dump

​	也是获取变量类型，比上面更详细

```
<?php
	$a = 10;
	$b = null;
	$c = 'wh';

	echo var_dump($a);	//int(10)
	echo var_dump($b);	//NULL 
	echo var_dump($c);	//string(2) "wh" 
	echo var_dump($d);	//NULL
?>


如果传参不加$
<?php
	$a = 10;
	$b = null;
	$c = 'wh';

	echo var_dump(a);	//string(1) "a"
	echo var_dump(b);	//string(1) "b" 
	echo var_dump(c);	//string(1) "c" 
	echo var_dump(d);	//string(1) "d"
?>
```



##### isset

​	测试变量是否存在

​	echo打印布尔变量，如果布尔为true，就打印为1，如果布尔为false，就打印空内容，所以不适合

```
<?php
	$a = 1;
	var_dump(isset($a))		因为echo不适合打印布尔，所以用这个
?>

变量就没定义		 false
变量为null		   false
其他的都是true
```



##### empty

​	测试变量是否为空

```
<?php
	$a = 1;
	var_dump(empty($a))		
?>

变量没有定义		true
变量为null		  true
变量为空字符串	   true
变量为'0'		  true
变量为空数组		true
变量为0		  true
变量为0.0		  true
变量为false	  true
其他都是false
```





#### 自动类型转换

##### 	字符串转数值型

```
<?php
	$a = '20abc';	如果字符串前面是数字开头，那么就以开头的数字转换成整型，否则当成0
	echo $a+10;	//30
?>

<?php
	$a = 'abc';
	echo $a+10;	//10
?>

<?php
	$a = 'abc5';
	echo $a+10;	//10
?>

<?php
	$a = '1abc5';
	echo $a+10;	//11
?>
```



##### 	数值型转字符串

```
<?php
	$a = 10;
	echo "the number is ".$a;	//the number is 10
	echo "the number is ".a;	//the number is a
?>
```



##### 	其他类型转布尔型

```
0				false
0.0				false
''				false
'0'				false
null			false
没有定义		 false
array()			false
false			false

自动类型转换，可以用于if条件判断
```



#### 强制类型转换



```
<?php
	$a = array('hello','world');
	$b = (string)$a;
	var_dump($b);	//string(5) "Array"
	echo "$b";		//Array
?>

如果其他类型要转成字符串，那么其他类型使用echo打印出什么，就是转换后的字符串的内容
```

```
<?php
	$a = "10.55abc";
	$b = (int)$a;	//10
	$b = (float)$a;	//10.55
	echo $b;
?>
```

##### is_scalar

​	是否为标量，也就是是否为基本类型

```
<?php
	function y(){

	}

	$a = is_scalar(0);			//true
	$b = is_scalar(0.0);		//true
	$c = is_scalar('0');		//true
	$d = is_scalar(array());	//false
	$e = is_scalar(null);		//false


	var_dump($a);
	var_dump($b);
	var_dump($c);
	var_dump($d);
	var_dump($e);
?>
```



##### is_object

​	只有class是object

##### is_callable

​	function定义的

```
<?php
	function y(){

	}

	$a = is_callable(y); 
	var_dump($a);		//true
?>

<?php
	function y(){

	}

	$a = is_callable('y');
	var_dump($a);		//true
?>
```

