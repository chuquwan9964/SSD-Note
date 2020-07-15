# JUC

java.util.concurrent



##### CopyOnWriteArrayList

​	写时复制，在读操作较多，写操作较少情况下适用

​	两个缺点

​		内存消耗较大，每次写操作都会拷贝一个新的数组在内存中

​		不能保证数据的实时一致性，但是保证数据的最终一致性，为什么呢？

​			当一个线程要读此集合时，由于读不加锁，所以线程拿到的是集合当前的底层数组，如果这时有另一个线程来写此集合，另一个线程会先拷贝此集合，在拷贝的集合上做写操作，最后将集合的引用指向拷贝后的集合，因为读和写不是同一个对象(在堆中)，所以读写互不影响，所以没有保证数据的实时一致性

​		它的迭代器iterator也是弱一致性，不会抛出ConcurrentModificationException

```
CopyOnWriteArrayList:CopyOnWriteArrayList这是一个ArrayList的线程安全的变体，其原理大概可以通俗的理解为:初始化的时候只有一个容器，很常一段时间，这个容器数据、数量等没有发生变化的时候，大家(多个线程)，都是读取(假设这段时间里只发生读取的操作)同一个容器中的数据，所以这样大家读到的数据都是唯一、一致、安全的，但是后来有人往里面增加了一个数据，这个时候CopyOnWriteArrayList 底层实现添加的原理是先copy出一个容器(可以简称副本)，再往新的容器里添加这个新的数据，最后把新的容器的引用地址赋值给了之前那个旧的的容器地址，但是在添加这个数据的期间，其他线程如果要去读取数据，仍然是读取到旧的容器里的数据。
```



##### CopyOnWriteArraySet

##### ConcurrentHashMap



##### ThreadLocalRandom

​	多线程情况下适用的随机数生成器

​	原理是在各自的Thread里有threadLocalRandomSeed变量，避免了多个线程公用一个种子导致的线程自旋加大性能消耗的问题

​	因为生成随机数是要使用旧种子生成新种子，再用新种子生成随机数的，因为Random的seed是原子类，所以当多个线程并发访问时，会因为访问失败而自旋



##### AtomicLong

​	底层实现原理为cas

​	unsafe是真正干活的，它可以像c一样直接访问内存，但是官方不推荐开发者使用，可以使用反射使用

##### LongAdder

​	为了解决高并发的AtomicLong

​	内部有一个AtomicLong类型的base，还有cells数组，每个线程都可以通过特定函数算出自己的cell进行操作

​	还是来说一下原理，以便以后忘掉

​		如果同一时间内只有一个线程访问，那么它只会使用cas算法增加base的值

​		当同时有多个线程访问时，它会启用cells数组，算出此线程的cell位置并进行cas

​		如果用自己的cell还是cas运算失败时，那么就会重试，重试不成功就会扩容cells数组，扩容2倍

##### 测试并发速度

```
public class LongAdderTest {
//    static Lock lock = new ReentrantLock();

    static class MyLong {
        long a;

        void inc() {
            a++;
        }

        long get() {
            return a;
        }
    }

    /**
     * 开启10个线程对原子类进行自增操作，每个线程五千万次，看看谁的时间短
     *
     * @param args
     * @throws InterruptedException
     */
    public static void main(String[] args) throws InterruptedException {
        Thread[] threads = new Thread[10];
        AtomicLong l1 = new AtomicLong();//11s
        LongAdder l2 = new LongAdder();//2s
        MyLong l3 = new MyLong();//加lock后是15s,加synchronized后是21s
        long begin = System.currentTimeMillis();
        for (int i = 0; i < 10; i++) {
            Thread t = new Thread(() -> {
                for (int j = 0; j < 50000000; j++) {
                    synchronized ("1"){
                        l3.inc();
                    }
                }
            });
            t.start();
            threads[i] = t;
        }
        for (int i = 0; i < 10; i++) {
            threads[i].join();
        }
        long end = System.currentTimeMillis();
        System.out.println(end - begin);
        System.out.println(l3.get());
    }
}
```



##### LongAccumulator

​	accumulator	累加的意思

​	LongAdder是它的一个特例，他可以自定义计算规则

![58185998130](H:\笔记\images\1581859981309.png)



![58185999701](H:\笔记\images\1581859997010.png)



![58186012619](H:\笔记\images\1581860126190.png)



```
上图，定义初始值为0，计算规则为乘的累加器，具有并发效率和原子特性
当计算规则为空时，默认为加法计算
```





##### LockSupport

​	工具类

​	它的主要作用是挂起和唤醒线程

​	该工具类是创建锁和其他同步类的基础。

​	LockSupport 类与每 使用它的线程都会关联一 个许可证，在 默认情况下调用

​	LockSupport 类的方法的线程是不持有许可证的。 LockSupport 是使用 Unsafe 类实现的



###### park()

​	默认情况下当前线程没有许可证，于是调用park阻塞

![58193059902](H:\笔记\images\1581930599020.png)



![58193078304](H:\笔记\images\1581930783041.png)



###### unPark(Thread t)

​	调用此方法可以使参数线程拿到许可证，也可以唤醒没有许可证但是调用park了的线程

![58193096675](H:\笔记\images\1581930966755.png)



![58193098521](H:\笔记\images\1581930985219.png)



**unpark后只能park通过一次**

![58193281956](H:\笔记\images\1581932819567.png)

![58193286154](H:\笔记\images\1581932861545.png)



1. unpark调用时，如果当前线程还未进入park，则许可为true
2. park调用时，判断许可是否为true，如果是true，则继续往下执行；如果是false，则等待，直到许可为true



###### interrupt()

![58193114214](H:\笔记\images\1581931142140.png)



###### parkNanos(long nanos)

​	阻塞nanos毫秒后返回

###### park(Object blocker)

​	当线程在没有持有许可证的情况下调用 park 方法而被阻塞挂起时 ，这个 blocker 象会被记录到

该线程内部

###### getBlocker

​	