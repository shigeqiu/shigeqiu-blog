# Object方法

- [Object方法](#object方法)
  - [notify和wait](#notify和wait)
    - [notify 和 notifyAll的区别](#notify-和-notifyall的区别)
  - [clone和finalize](#clone和finalize)

``` java
public class Object {
    public final native Class<?> getClass();
    public native int hashCode();
    public boolean equals(Object obj) {
        return (this == obj);
    }
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
    public final native void notify();
    public final native void notifyAll();
    public final native void wait(long timeout) throws InterruptedException;
    
    public final void wait() throws InterruptedException {
        wait(0);
    }

    public final void wait(long timeout, int nanos) throws InterruptedException {
        if (timeout < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }

        if (nanos > 0) {
            timeout++;
        }

        wait(timeout);
    }


    protected void finalize() throws Throwable { }
    protected native Object clone() throws CloneNotSupportedException;

}
```


## notify和wait

``` java
/** 
* 计算输出其他线程锁计算的数据
* 
*/ 
public class ThreadA {
    public static void main(String[] args) throws InterruptedException{
        ThreadB b = new ThreadB();
        //启动计算线程
        b.start(); 
        //线程A拥有b对象上的锁。线程为了调用wait()或notify()方法，该线程必须是那个对象锁的拥有者
        synchronized (b) {
            System.out.println("等待对象b完成计算。。。");
            //当前线程A等待
            b.wait();
            System.out.println("b对象计算的总和是：" + b.total);
        } 
    } 
}

 
/** 
* 计算1+2+3 ... +100的和
* 
*/ 
class ThreadB extends Thread {
    int total; 

    public void run() {
        synchronized (this) {
            for (int i = 0; i < 101; i++) {
                total += i; 
            } 
            //（完成计算了）唤醒在此对象监视器上等待的单个线程，在本例中线程A被唤醒
            notify(); 
            System.out.println("计算完成");
        } 
    } 
}

```

void notify() 唤醒在此对象监视器上等待的单个线程。 

void notifyAll() 唤醒在此对象监视器上等待的所有线程。 


void wait() 导致当前的线程等待，直到其他线程调用此对象的 notify() 方法或 notifyAll() 方法。 

void wait(long timeout) 导致当前的线程等待，直到其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者超过指定的时间量。 

void wait(long timeout, int nanos) 导致当前的线程等待，直到其他线程调用此对象的 notify()



当线程执行wait()方法时候，会释放当前的锁，然后让出CPU，进入等待状态。
只有当 notify/notifyAll() 被执行时候，才会唤醒一个或多个正处于等待状态的线程，然后继续往下执行，直到执行完synchronized 代码块的代码或是中途遇到wait() ，再次释放锁。

也就是说，notify/notifyAll() 的执行只是唤醒沉睡的线程，而不会立即释放锁，锁的释放要看代码块的具体执行情况。所以在编程中，尽量在使用了notify/notifyAll() 后立即退出临界区，以唤醒其他线程 

4、wait() 需要被try catch包围，中断也可以使wait等待的线程唤醒。

5、notify 和wait 的顺序不能错，如果A线程先执行notify方法，B线程在执行wait方法，那么B线程是无法被唤醒的。


wait()方法是让线程释放对象锁，让其他线程拿到锁之后去优先执行，当其他全程唤醒wait()中的线程 或者 拿到对象锁的线程都执行完释放了对象锁之后，wait()中的线程才会再次拿到对象锁从而执行。

sleep()方法是让线程睡眠，此时并没有释放对象锁，其他想要拿到睡眠线程的对象锁的线程也就拿不到相应的对象锁，从而不能抢在它前面执行


### notify 和 notifyAll的区别

notify方法只唤醒一个等待（对象的）线程并使该线程开始执行。所以如果有多个线程等待一个对象，这个方法只会唤醒其中一个线程，选择哪个线程取决于操作系统对多线程管理的实现。notifyAll 会唤醒所有等待(对象的)线程，尽管哪一个线程将会第一个处理取决于操作系统的实现。如果当前情况下有多个线程需要被唤醒，推荐使用notifyAll 方法。比如在生产者-消费者里面的使用，每次都需要唤醒所有的消费者或是生产者，以判断程序是否可以继续往下执行。



## clone和finalize

protected Object clone() 创建并返回此对象的一个副本。 

protected void finalize() 当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法。 子类重写 finalize 方法，以配置系统资源或执行其他清除。

