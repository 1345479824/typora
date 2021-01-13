java8新特性

Lambda表达式  箭头->  lambda表达式需要实现接口

箭头的左侧：参数列表                            （接口中方法的参数）

箭头的右侧：方法的实现                          （接口中方法的实现）

语法格式1：无参数，无返回值

()->System.out.prinl("hello world");

语法格式2：有1个参数，无返回值；和无参无返回值差不多

语法格式3：只有一个参数  ->左边的小括号可以不写

语法格式4：有两个以上的参数，并Lambda体中有多条语句,函数体必须使用{  }

（x,y）->{

​					system.out.prinl("hello");

​					return 0;

​				}

语法格式5：只有一条语句  {}和return都可以省略不写

语法格式6：参数列表的数据类型可以省略不写，jvm编译器可以根据上下文推断出类型

Lamba表达式需要“函数式接口”的支持

函数式接口：接口中只有一个抽象方法的接口，称为函数式接口。该接口上方可以使用注解@FunctionalInterface修饰，可以检查是否是函数式接口





stream API





Runnable接口 可用于共享变量

daemon(守护线程)  thread1.setDaemon(true)设置thread1为守护线程  。Main线程是个非守护线程，不能设置成守护线程。Main线程结束，其他线程一样可以正常运行。 当线程只剩下守护线程的时候，JVM就会退出；如果还有其它任意一个用户线程还在，JVM就不会退出。thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个IllegalThreadStateException异常；在Daemon线程中产生的新线程也是Daemon的；

thread1.setPriority  设置线程优先级  优先级   : 只能反映 线程 的 中或者是 紧急程度 , 不能决定 是否一定先执行

main方法里写thread1.join(); thread2.join();  main线程会等到thread1和thread2全部执行完才执行  但是thread1和thread2线程会交替执行

thread1.interrept 给thread1打断点，若thread1在sleep或者wait或者join状态，则thread1会抛出一个异常，并且会打断thread1的sleep或者wait或者join状态。若不在sleep或者wait或join状态，则只会打个断点

强制关闭一个线程的策略: 1、设置一个flag，通过另外一个线程将flag从true改为false关闭该线程。2、建立一个线程A，将需要关闭的线程B设置为这个线程的子线程，利用A来关闭B线程

多线程中的可见性问题。

synchronized和volatile的比较 ：1.volatile不需要加锁，比synchronized更轻量级，不会阻塞线程 2.从内存可见性角度讲，volatile读操作=进入synchronized代码块（加锁），volatile写操作= 退出synchronized代码块（解锁） 3.synchronized既能保证可见性，又能保证原子性，而volatile只能保证可见性，不能保证原子性。

synchronized（Object）给线程加锁 ，相当于使得线程串行化执行，效率很低

public synchronized void run()  给run方法加锁  若3个线程同时抢占cpu资源，则先抢占到的线程会将run方法执行完，其它两个线程才有可能抢到锁。  synchronized给方法加锁默认加的是this锁

LOCK锁比synchronized锁效率高点

静态代码块加锁(即class锁)，某个线程要执行该类的某个方法时，要先等待10秒，因为给静态代码块加了锁，静态代码块先执行。

​		static{

​						synchronized(该类类名.class){

​												Thread.sleep(10_000);

​				}

​				}

加锁尽量可能小粒度，否则很影响效率。



wait 、notify线程通信

notify：唤醒某个锁等待队列的其中一个线程

wait：会释放当前线程的锁，并将当前线程加入到该锁的等待队列，需要notify进行唤醒

单生产者消费者问题，假设每次只生产一样东西：当生产者生产产品时，发现仓库没有商品，便生产商品，然后通知消费者消费产品，然后进入wait状态，进入wait状态才可以释放锁。消费者获得锁，发现有商品，就消费商品，通知生产者，进入wait状态，释放锁。。。。。。生产者获得锁之后，会接着从wait开始执行。。。。循环往复

第25讲多生产者多消费者造成的的假死问题   

p1创造完后进入wait状态，然后notify了p2；
p2被唤醒后发现已经创造了，就直接进入wait状态；
c2发现可以消费，就先消费然后进入wait状态，并notify了c1；
c1被唤醒后发现不能消费就直接进入wait状态；
这时p1,p2,c1,c2都为wait状态，程序就卡主了。

第26章  notifyAll()下的多生产者多消费者问题，若将代码块中的while换成if可能会造成重复消费

第27章  sleep和wait的区别 

1、 sleep是Thread中的方法，但是wait是Object的锁

2、sleep不会释放锁，但是wait将释放锁，并把该线程加入到这个锁的队列中

3、sleep不需要定义synchronized方法，但是wait需要

4、sleep方法不需要被唤醒，但是wait需要。      wait（10）这种会自动唤醒该线程

第28、29、30、31、33、34 36 37 38章 跳过！！



35章  

线程池要考虑的事情：1、任务队列 2、拒绝策略（抛出异常、阻塞、放到临时队列)3、线程池初始化的大小

4、active 5、线程池最大的大小    min<=active<=max



45章  内存可见性问题（缓存不一致问题）

volatile不保证原子性，但是会保证内存可见性，volatile的底层协议（实现）是MESI协议。

当某个共享变量不加volatile时，两个线程同时操作该变量时，则该变量的值会暂时存储在cpu和主存中间的cache中（这两个线程的cache不是同一个cache），若后面只有读操作，则会发生内存不可见问题（这是jvm的优化，当某个线程只读取该变量，而不发生写的操作时，则该线程会从cache中读取该变量的值）。

例子：

```java
public class VolatileTest {
   private static int INIT_VALUE = 0;

   private final static int MAX_LIMIT = 5;

   public static void main(String[] args) {
      new Thread(()->{
         int localValue = INIT_VALUE; //INIT_VALUE为只读操作
         while (localValue < MAX_LIMIT){
            if(localValue != INIT_VALUE){ ////INIT_VALUE为只读操作
               System.out.printf("The value is [%d]\n",INIT_VALUE);
               localValue = INIT_VALUE;
            }
         }
      },"READER").start();
      new Thread(new Runnable() {
         @Override
         public void run() {
            int localValue = INIT_VALUE;
            while (INIT_VALUE < MAX_LIMIT){
               if(localValue == INIT_VALUE){
                  System.out.printf("Update the value to [%d]\n",++localValue);
                  INIT_VALUE = localValue;
               }
               try {
                  Thread.sleep(100);
               } catch (InterruptedException e) {
                  e.printStackTrace();
               }
            }
         }
      },"WRITER").start();
   }
}
输出结果：
Update the value to [1]
Update the value to [2]
Update the value to [3]
Update the value to [4]
Update the value to [5]

```

若两个线程都有写的操作，则不会发生内存不可见问题，因为有写的操作时，jvm不会做优化，cpu每次读取数据要从主存读，然后将修改后的数据先写回cache再写回主存中。

例子:

```java
public class Test2 {
    private static int INITVALUE = 0;
    private static final int MAX = 50;

    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                while (INITVALUE < MAX){
                    System.out.println(Thread.currentThread().getName() + "输出" + ++INITVALUE);
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                while (INITVALUE < MAX){
                    System.out.println(Thread.currentThread().getName() + "输出" + ++INITVALUE);
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();
    }
}
```



46章  保证缓存一致性的两种解决办法

1.给数据总线加锁（线程串行化，效率低下）

​			总线（数据总线、地址总线、控制总线）  

2、cpu高速缓存一致性协议（Intel MESI协议）（现在大量使用 ，并行，效率高）

核心思想：

1、当cpu写入数据时，如果发现该变量被共享（也就是说，在其他cpu中也存在该变量的副本），则会发送一个信号，通知其他cpu该变量的缓存无效，直接从主存读取

2、当其他cpu访问该变量的时候，重新到主存进行获取



第47章

并发编程中三个比较重要的概念

 1、原子性 （一个操作或者多个操作要么都成功要么都失败，中间不能由于任何的因素中断）

2、可见性（内存可见性）

3、有序性（顺序性）

可能会重排序 ， 重排序只要求最终一致性 ，有依赖关系的不能重排序！



第48章

1、原子性

对于基本数据类型的变量读取和赋值是保证了原子性的，要么都成功，要么都失败，这些操作不可以被中断

以i=10语句来说

cache和memory都会被置成10 ，是原子性的

a=10   原子性

b=a   不是原子性  cpu先读取a的值，再置到b中   需要两步

c++  不是原子性

2、可见性

​			使用volatile保证可见性 （不保证原子性）

3、有序性

   	 	happens-before原则：

​    3.1 代码的执行顺序，编写在前面的发生在编写在后面的

​    3.2  unlock必须发生在lock之后

​    3.3  volatile修饰的变量，对该变量的写操作必须发生在该变量的读操作之后

​	3.4  传递规则，操作A先与B，B先于C，那么A肯定先于C

​    3.5  线程启动规则，start方法肯定先于线程run

​    3.6 线程中断规则，interrupt必须发生在捕获该动作之前

​	3.7  对象销毁规则，初始化必须发生在finalize之前

​    3.8 线程终结规则：所有的操作都发生在线程死亡之前

try{	

​	lock.lock();

}finally{

​	lock.unlock();

}

49章

volatile关键字

一旦一个共享变量被volatile修饰，具备两层语义

1.保证了不同线程间的可见性（通过高速缓存的一致性协议保证）

2.禁止对其进行重排序，保证了有序性（通过内存屏障保证）

3.未保证原子性

该图表示未保证原子性，可以加lock锁 或者加synchronized关键字

![1587524007116](E:\Typora笔记\多线程\assets\1587524007116.png)

volatile关键字

1.保证重排序的时候不会把后面的指令放到屏障前面，也不会把前面的放到后面

举例：volatile int a ； int b; int c;

b =2;  a = 1; c = 1;    在a=1之前，b=2一定已经执行完毕；一定要a=1执行完后，c=1才执行



2.强制对缓存的修改操作立刻写入主存

3.如果是写操作，会导致其他cpu中的缓存失效

50章 

volatile的使用场景

1、状态量标记  （使用最多的场景）

volatile boolean start = true；

while（start）{}

void close（）{}

2.屏障前后的一致性

总结前面章节 

1.cpu的大致结构

2.jvm的大致结构

3.缓存一致性协议

4.指令重排序

5.happens-before规则

6.并发编程的三大要素

7.volatile关键字的作用

51章  观察者模式

52章  不会

53、54章 

55 56 57章

读写分离 ，多个线程同时读的操作允许不加锁  其它情况需要加锁 （读写分离）

58章 

无锁并发编程 

1.不可变对象一定是线程安全的

2.可变对象不一定是不安全的

不可变对象定义：1.没有set方法

​								2.所有变量为final、private

​								3.该类不能被继承（加final）	

​								4.一个实例引入了一个可变的对象，不允许改变

不可变对象容易忽略一些东西 比如

public class A{

​	private final List<Stirng> list;

​    public List getList(){

​				/**虽然list是final的，但是list中的内容还是会被改变，需要通过clone或者

​					return Collections.unmodifiableList(list)解决问题

​				**/

​				return list;

​	}

}

好处 ：避免加锁的步骤，提高效率，线程越多效率越低下，串行化严重

59章

string是不可变对象，线程安全   stringBuffer是可变对象，线程安全，加了很多synchronized锁    stringbuilder不加锁，线程不安全

60章 future （未来者）设计模式   相当于异步操作 

61章 通过回调实现未来者设计模式  回调方式是特殊的观察者模式，一个被观察者通知一个观察者。 观察者模式是一个被观察者通知多个观察者

62章 回顾

63~79章   战略性跳过 不会



