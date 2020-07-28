## Kotlin

#### 1.hello world

```kotlin
package day01

fun main(args:Array<String>) {
    println("hello kotlin!!!")
}
```

#### 2.基础知识

##### 2.1变量的声明与输出

```kotlin
package day01

fun main(args:Array<String>) {
    //声明变量
    var name = "sjh"
    println(name)
    
    
    //声明常量
    val constValue = "const"
}
```

##### 2.2变量的类型推断

```kotlin
package day01

fun main(args:Array<String>) {
	var i = 10
    i = 999999999//不行，因为i已经被推断为了Int类型
    
    
    var b	//不行，因为没有给b指定类型
    
    var c:Int	//可以，因为指定了类型
}
```



##### 2.3变量类型

##### 2.4函数

```kotlin
fun func_name(arg1:type1,arg2:type2) {
    //func_body
}
```



##### 2.5字符串模板

##### 2.6流程控制





