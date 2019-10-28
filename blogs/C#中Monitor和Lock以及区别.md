### 一、Monitor
1. Monitor.Enter(object)方法是获取锁，Monitor.Exit(object)方法是释放锁，这就是Monitor最常用的两个方法，当然在使用过程中为了避免获取锁之后因为异常，致锁无法释放，所以需要在try{} catch(){}之后的finally{}结构体中释放锁(Monitor.Exit())。

2. Monitor的常用属性和方法：
* Enter(Object) 在指定对象上获取排他锁。
* Exit(Object) 释放指定对象上的排他锁。
* IsEntered 确定当前线程是否保留指定对象锁。
* Pulse 通知等待队列中的线程锁定对象状态的更改。
* PulseAll 通知所有的等待线程对象状态的更改。
* TryEnter(Object) 试图获取指定对象的排他锁。
* TryEnter(Object, Boolean) 尝试获取指定对象上的排他锁，并自动设置一个值，指示是否得到了该锁。
* Wait(Object) 释放对象上的锁并阻止当前线程，直到它重新获取该锁。

### 二、Lock关键字
1. Lock关键字实际上是一个语法糖，它将Monitor对象进行封装，给object加上一个互斥锁，A进程进入此代码段时，会给object对象加上互斥锁，此时其他B进程进入此代码段时检查object对象是否有锁？如果有锁则继续等待A进程运行完该代码段并且解锁object对象之后，B进程才能够获取object对象为其加上锁，访问代码段。
2. Lock关键字封装的Monitor对象结构如下：

    ````
    lock（obj）
    {
    }
    ````
    等价于：

    ````
    try
    {
        Monitor.Enter(obj);
    }
    catch(Exception ex)
    {
        
    }
    finally
    {
        Monitor.Exit(obj);
    }
    ````

3. 锁定的对象应该声明为private static object obj = new object();尽量别用公共变量和字符串、this、值类型。

### 三、Monitor和Lock的区别
1. Lock是Monitor的语法糖。
2. Lock只能针对引用类型加锁。
3. Monitor能够对值类型进行加锁，实质上是Monitor.Enter(object)时对值类型装箱。
4. Monitor还有其他的一些功能。

>完整示例
````
class Program
{
    private static object obj = new object();
    public void LockSomething()
    {
        lock (obj)
        {
            dosomething();
        }
    }
    public void MonitorSomeThing()
    {
        try
        {
            Monitor.Enter(obj);
            dosomething();
        }
        catch(Exception ex)
        {
            
        }
        finally
        {
            Monitor.Exit(obj);
        }
    }

    public void dosomething()
    { 
        //做具体的事情
    }
}
````

> Pulse() / Wait() 示例

````
public class Program
{
    public static void Main(string[] args)
    {
        LockObj obj = new LockObj();

        //注意，这里使用的是同一个资源对象obj
        Jack jack = new Jack(obj);
        John john = new John(obj);

        Thread t1 = new Thread(new ThreadStart(jack.Run));
        Thread t2 = new Thread(new ThreadStart(john.Run));

        t1.Start();
        t1.Name = "Jack";

        t2.Start();
        t2.Name = "John";

        Console.ReadLine();
    }
}
 
//锁定对象
public class LockObj { }

public class Jack
{
    private LockObj obj;

    public Jack(LockObj obj)
    {
        this.obj = obj;
    }

    public void Run()
    {
        Monitor.Enter(this.obj);

        Console.WriteLine("{0}:今天我值班吗？看下", Thread.CurrentThread.Name);

        Console.WriteLine("{0}：原来是jon值班呀，那我走呀", Thread.CurrentThread.Name);

        //暂时的释放锁资源
        Monitor.Wait(this.obj);

        Console.WriteLine("{0}：那行吧", Thread.CurrentThread.Name);

        //唤醒等待队列中的线程
        Monitor.Pulse(this.obj);

        Console.WriteLine("{0}：呵呵", Thread.CurrentThread.Name);

        Monitor.Exit(this.obj);
    }
}

public class John
{
    private LockObj obj;

    public John(LockObj obj)
    {
        this.obj = obj;
    }

    public void Run()
    {
        Monitor.Enter(this.obj);

        Console.WriteLine("{0}:哥们，今天我有点事情，你帮我下呗", Thread.CurrentThread.Name);

        //唤醒等待队列中的线程
        Monitor.Pulse(this.obj);

        Console.WriteLine("{0}:谢谢兄弟了", Thread.CurrentThread.Name);

        //暂时的释放锁资源
        Monitor.Wait(this.obj);

        Console.WriteLine("{0}：哈哈", Thread.CurrentThread.Name);

        Monitor.Exit(this.obj);
    }
}
````


> refer

* [C#中Monitor和Lock以及区别](https://www.cnblogs.com/soundcode/p/9100646.html)
* [Monitor锁](https://www.cnblogs.com/mengluo/p/5599665.html)
* [C# 线程同步之排它锁/Monitor监视器类](https://www.cnblogs.com/tianma3798/p/6290712.html)
