![1588062790339](E:\Typora笔记\设计模式\assets\1588062790339.png)



工厂模式注重对象的创建，建造者模式注重业务过程



### 1、单例模式

```java
/**
 * 不能够懒加载，因为类加载时instance同时被加载到内存
 */
public class SingletonObject1 {
	private static final SingletonObject1 instance = new SingletonObject1();

	private SingletonObject1(){}

	public static SingletonObject1 getInstance(){
		return instance;
	}
}

/**
 * 多线程情况下可能会出现不止一个实例
 */

public class SingletonObject2 {

	private static SingletonObject2 instance;

	private SingletonObject2(){}

	public static SingletonObject2 getInstance(){
		if(null == instance){
			instance = new SingletonObject2();
		}
		return SingletonObject2.instance;
	}
}

/**
 * 懒加载其实就是延时加载,即当对象需要用到的时候再去加载。
 * 解决了懒加载、多实例的问题
 * 但是除了第一个线程外是创建实例，其他线程都是读的操作，
 * 都需要等待前一个线程完成读操作，影响性能
 */

public class SingletonObject3 {

	private static SingletonObject3 instance;

	private SingletonObject3(){}

	public synchronized static SingletonObject3 getInstance(){
		if(null == instance){
			instance = new SingletonObject3();
		}
		return SingletonObject3.instance;
	}
}
/**
 * 懒加载其实就是延时加载,即当对象需要用到的时候再去加载。
 * 解决了性能问题
 *
 * 可能会出现NullPointException
 */

public class SingletonObject4 {

	private static SingletonObject4 instance;
//	private Object obj;
//  public Object getObj(){}
	private SingletonObject4(){
		// do some heavy things
		// int i=0, j=10;
		// obj = new Object();
		// ...
	}

	public static SingletonObject4 getInstance(){
		if(null == instance){
			synchronized (SingletonObject4.class){
				if(null == instance){
					/**
					 * 可能在线程A构造函数已经返回引用时，构造函数内的成员变量还未初始化完成，当线程B获得锁，
					 * 并且判断instance！=null，调用instance的getObj()，并使用obj内的方法时，会出现空指针异常，
					 * （instance这时候为null）
					 *
					 * 正确的顺序应该是构造函数的语句先执行，然后将对象的引用返回
					 */
					instance = new SingletonObject4();
				}
			}
		}
		return SingletonObject4.instance;
	}
}

/**
 * 可以解决空指针异常
 * 一个线程A可能new SingletonObject5()的时候，还没有构造完成，就将instance返回 ，另外一个线程B使用这个实例的时候，
 * 如果构造函数有引用变量还没初始化完成，线程B去调用这个引用变量，就会引发空指针异常
 */
public class SingletonObject5 {
	/**
	 * 加了volatile关键字，保证了有序性（通过内存屏障保证），所以不会发生空指针异常，因为加了instance关键字之后，
	 * 会保证在SingletonObject5函数内的语句执行完之后，才将SingletonObject5类对象的引用赋给instance
	 */
	private static volatile SingletonObject5 instance;

	private SingletonObject5(){}

	// double check and volatile
	public static SingletonObject5 getInstance(){
		if(null == instance){
			synchronized (SingletonObject5.class){
				if(null == instance){
					instance = new SingletonObject5();
				}
			}
		}
		return SingletonObject5.instance;
	}
}
/**
 * 静态内部类中的静态变量Instance在类加载初始化阶段就已经创建好了
 * 实现了懒加载、线程安全，性能也比较高
 */
public class SingletonObject6 {
	private SingletonObject6(){}

	private static class InstanceHolder{
		//final保证了SingletonObject6类对象是同一个,static保证了最先被加载
		private static final SingletonObject6 Instance = new SingletonObject6();
	}

	public static SingletonObject6 getInstance(){
		return InstanceHolder.Instance;
	}
}
/**
 * 实现了懒加载、线程安全，性能也比较高
 */
public class SingletonObject7 {
	private SingletonObject7(){}
	private enum Singleton{
		B,  //这个B没啥用的
		INSTANCE;  //Singleton.INSTANCE 相当于调用Singleton()构造方法
		private final SingletonObject7 instance;
		Singleton(){
			instance = new SingletonObject7();
		}

		//public SingletonObject7 getInstance(){
		//	return instance;
		//}
	}
	public static SingletonObject7 getInstance(){
		return Singleton.INSTANCE.instance;
	}
}

最后两种方法实现单例懒加载是最好的 并且不存在线程安全问题

```

