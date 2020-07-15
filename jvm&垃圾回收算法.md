## JVM

java内存模型(jmm)和java内存结构不一样

java内存模型

​	java memory model

​	跟多线程有关

### java内存结构



#### 	堆

​		在Java虚拟机启动的时候建立Java堆，它是Java程序最主要的内存工作区域，几乎所有的对象实例都									     存放到Java堆中，堆空间是所有线程共享。

​	

​	新生代和老年代

​		当一个对象使用的次数非常多时，就将放入了老年代

​		垃圾回收机制在新生代区域回收较多，在老年代区域回收较少

##### RuntimeAPI

​	long	maxMemory()

​		堆的最大可用内存

​	long	freeMemory()

​		堆当前剩余内存

​	long	totalMemory()

​		堆当前可用的最大内存(如果满了就会继续要，直到比maxMemory大)

```
package com.chuquwan.jvm;

public class Demo1 {

    public static void main(String[] args) throws InterruptedException {
        Runtime runtime = Runtime.getRuntime();
        long l = runtime.maxMemory();
        System.out.println("maxMemory" + l / 1024 / 1024 + "M");//2014M

        long l1 = runtime.freeMemory();
        System.out.println("freeMemory" + l1 / 1024 / 1024 + "M");//123M

        long l2 = runtime.totalMemory();
        System.out.println("totalMemory" + l2 / 1024 / 1024 + "M");//126M

        System.out.println("准备睡3s挖个大的");

        Thread.sleep(3000);

        byte[] bytes = new byte[120 * 1024 * 1024];//120m

        System.out.println("---------------------------------------------------------");

        long l3 = runtime.maxMemory();
        System.out.println("maxMemory" + l3 / 1024 / 1024 + "M");//2014M

        long l4 = runtime.freeMemory();
        System.out.println("freeMemory" + l4 / 1024 / 1024 + "M");//3M

        long l5 = runtime.totalMemory();
        System.out.println("totalMemory" + l5 / 1024 / 1024 + "M");//126M


    }
}
```







##### 堆的参数配置

```
-XX:+PrintGC      每次触发GC的时候打印相关日志
-XX:+UseSerialGC      串行回收
-XX:+PrintGCDetails  更详细的GC日志
-Xms               堆初始值
-Xmx               堆最大可用值
-Xmn               新生代堆最大可用值
-XX:SurvivorRatio     用来设置新生代中eden空间和from/to空间的比例
-XX:NewRatio		设置老年代:新生代的比例，比如3，就代表老年代是新生代的3倍
-Xss				设置栈内存大小，可以增加栈的最大深度
含以-XX:SurvivorRatio=eden/from=den/to
总结:在实际工作中，我们可以直接将初始的堆大小与最大堆大小相等，
这样的好处是可以减少程序运行时垃圾回收次数，从而提高效率。
-XX:SurvivorRatio     用来设置新生代中eden空间和from/to空间的比例.
```



###### jvm参数调优

```
-Xms1000m -Xmx1000m -XX:+PrintGCDetails -XX:+UseSerialGC -XX:+PrintCommandLineFlags

堆初始值为1000M，最大值为1000M，打印详细日志，串行回收
```

###### 新生代参数配置

​	除了可以设置新生代的绝对大小，也可以设置其与老年代的比例关系

```
-Xms20m -Xmx20m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC
-XX:NewRatio=3

#老年代大小是新生代大小的3倍，这样可以减少GC垃圾回收老年代的次数，增加GC回收新生代的次数
-XX:NewRatio=3
```

###### 堆内存溢出

​	调整堆内存大小，这里不做叙述，上面都有

###### 栈内存溢出

​	栈有最大深度

​	测试栈的最大深度

```
package com.chuquwan.jvm;

public class Demo3 {
    private static int count = 0;

    public static void addCount() {
        try {
            count++;
            System.out.println(count);
            addCount();
        } catch (Exception e) {
            System.out.println("最大深度" + count);
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        addCount();
    }
}

```



​	调整栈大小

```
-Xss5m
```











#### 	栈

​	为每个线程单独分配的内存空间，每个线程都有

​	存放方法中的局部变量，方法，每个线程私有，互不共享

##### 	栈帧

​		每个方法都有其栈帧

###### javap -c

​		反编译class文件

```
javap -c Demo3.class > Demo3.txt
javap -v	更详细的
```

<https://www.cnblogs.com/lsy131479/p/11201241.html>	JVM指令手册

```
Compiled from "Demo3.java"
public class com.chuquwan.jvm.Demo3 {
  public com.chuquwan.jvm.Demo3();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void addCount();
    Code:
    	#得到count的值
       0: getstatic     #2                  // Field count:I
       	#把1压入操作数栈
       3: iconst_1
       	#执行int类型的加法，也就是将操作数栈中的数与上面的count相加
       4: iadd
       	#将上面的结果设置到count
       5: putstatic     #2                  // Field count:I
       	#得到静态变量System.out
       8: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
       	#得到count，用于打印
      11: getstatic     #2                  // Field count:I
      14: invokevirtual #4                  // Method java/io/PrintStream.println:(I)V
      17: invokestatic  #5                  // Method addCount:()V
      20: goto          42
      23: astore_0
      24: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
      27: getstatic     #2                  // Field count:I
      30: invokedynamic #7,  0              // InvokeDynamic #0:makeConcatWithConstants:(I)Ljava/lang/String;
      35: invokevirtual #8                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      38: aload_0
      39: invokevirtual #9                  // Method java/lang/Exception.printStackTrace:()V
      42: return
    Exception table:
       from    to  target type
           0    20    23   Class java/lang/Exception

  public static void main(java.lang.String[]);
    Code:
       0: invokestatic  #5                  // Method addCount:()V
       3: return

  static {};
    Code:
       0: iconst_0
       1: putstatic     #2                  // Field count:I
       4: return
}

```



一个栈帧分为局部变量表，操作数栈，动态链接，方法出口

局部变量表:

​	存放局部变量的内存区域

操作数栈:

​	临时存放操作数的内存区域

动态链接:

​	调用对象方法时，有一个指针指向了该对象方法所在的方法区的地址

方法出口:

​	return

程序计数器记录下一条指令的地址，每个线程私有

```
iconst_1 	将int类型常量1压入栈(压入操作数栈)
istore_1 	将int类型值存入局部变量1(在局部变量表中开辟空间并将操作数栈的栈顶元素弹出并赋给前面开辟的)
iload_1		从局部变量1中装载int类型值(也就是说将局部变量表中1号变量取出并压入操作数栈)
iadd		从操作数栈中弹出两个元素并进行相加操作
bipush		将一个8位带符号整数压入栈
ireturn		从方法中返回int类型的数据
```







#### 	方法区

​		存放常量，静态变量，类元信息，字面量，常量池信息等，所有线程共享

​		使用的是直接物理内存空间

#### 	本地方法栈

​		用来调用c语言代码



#### 基本类型存储位置

```
 一：在方法中声明的变量，即该变量是局部变量，每当程序调用方法时，系统都会为该方法建立一个方法栈，其所在方法中声明的变量就放在方法栈中，当方法结束系统会释放方法栈，其对应在该方法中声明的变量随着栈的销毁而结束，这就局部变量只能在方法中有效的原因

      在方法中声明的变量可以是基本类型的变量，也可以是引用类型的变量。

         （1）当声明是基本类型的变量的时，其变量名及值（变量名及值是两个概念）是放在JAVA虚拟机栈中

         （2）当声明的是引用变量时，所声明的变量（该变量实际上是在方法中存储的是内存地址值）是放在JAVA虚拟机的栈中，该变量所指向的对象是放在堆类存中的。

   二：在类中声明的变量是成员变量，也叫全局变量，放在堆中的（因为全局变量不会随着某个方法执行结束而销毁）。

       同样在类中声明的变量即可是基本类型的变量 也可是引用类型的变量

       （1）当声明的是基本类型的变量其变量名及其值放在堆内存中的

       （2）引用类型时，其声明的变量仍然会存储一个内存地址值，该内存地址值指向所引用的对象。引用变量名和对应的对象仍然存储在相应的堆中
```





## 垃圾回收

#### 概述

​	垃圾回收机制就是不定时的向堆内存回收**不可达**对象

​	**可达性:**

​		从一系列被称为"GC ROOT"对象为起点，从这些节点向下搜索，节点所走过的路径成为引用链，当一个对象到GC ROOT没有任何引用链相连的时候，就会被视为不可达

```
package com.chuquwan.jvm;

public class Demo4 {

    public static void main(String[] args) throws InterruptedException {
        Demo4 demo4 = new Demo4();
        demo4 = null;	//将其设置为不可达对象，有很大几率会被回收
        System.gc();
    }

    @Override
    protected void finalize() throws Throwable {
        System.out.println("回收");
    }
}

```





#### 算法

##### 引用计数法

```
引用计数法：

给对象添加一个引用计数器，每当有一个地方引用它时，计数器值就加1，当引用失效时，计数器值就减1；任何时刻计数器为0的对象就是不可能再被使用的。

虽然引用计数法有许多成功的案例有很多，但是现在JVMGC算法最常用的算法中并没有使用这一算法（常用GC算法标记清除、标记压缩、复制算法），没有使用引用计数法的最主要原因可能是（head）头指针为null时，下面所有的元素都没有办法计数，也就是没有办法解决互相引用的的这一问题。
```



##### 复制算法

​	新生代

##### 	标记清除算法

​	老年代

##### 	标记压缩

​	标记清除压缩，简称标记整理

##### 	分代算法

