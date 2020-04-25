# wait和notify

<!-- TOC -->

- [wait和notify](#wait和notify)
    - [总结](#总结)
    - [notify 和 notifyAll的区别](#notify-和-notifyall的区别)
    - [wait没有获取锁的情况](#wait没有获取锁的情况)
    - [notify没有获取锁的情况](#notify没有获取锁的情况)
    - [wait和notify执行顺序情况](#wait和notify执行顺序情况)

<!-- /TOC -->

## 总结

1. **`wait()`、`notify/notifyAll()` 方法是Object的本地final方法，无法被重写。**
1. `wait()`使当前线程阻塞，前提是必须先获得锁，一般配合synchronized 关键字使用，一般在synchronized 同步代码块里使用 `wait()`、`notify/notifyAll()` 方法。
1. wait() 需要被try catch包围，中断也可以使wait等待的线程唤醒。
1. notify 和wait 的顺序不能错，如果A线程先执行notify方法，B线程在执行wait方法，那么B线程是无法被唤醒的。
1. 由于 wait()、notify/notifyAll() 在synchronized 代码块执行，说明当前线程一定是获取了锁的。

当线程执行wait()方法时候，会释放当前的锁，然后让出CPU，进入等待状态。只有当 notify/notifyAll() 被执行时候，才会唤醒一个或多个正处于等待状态的线程，然后继续往下执行，直到执行完synchronized 代码块的代码或是中途遇到wait() ，再次释放锁。

也就是说，notify/notifyAll() 的执行只是唤醒沉睡的线程，而不会立即释放锁，锁的释放要看代码块的具体执行情况。所以在编程中，尽量在使用了notify/notifyAll() 后立即退出临界区，以唤醒其他线程 



## notify 和 notifyAll的区别

notify方法只唤醒一个等待（对象的）线程并使该线程开始执行。所以如果有多个线程等待一个对象，这个方法只会唤醒其中一个线程，选择哪个线程取决于操作系统对多线程管理的实现。notifyAll 会唤醒所有等待(对象的)线程，尽管哪一个线程将会第一个处理取决于操作系统的实现。如果当前情况下有多个线程需要被唤醒，推荐使用notifyAll 方法。比如在生产者-消费者里面的使用，每次都需要唤醒所有的消费者或是生产者，以判断程序是否可以继续往下执行。

## wait没有获取锁的情况

``` java
public class BB {
    private void bb() {
        try {
            this.wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("bbbb");
    }

    public static void main(String[] args) {
        new BB().bb();
    }
}
```
异常：非法监视器状态异常
```
Exception in thread "main" java.lang.IllegalMonitorStateException
	at java.lang.Object.wait(Native Method)
	at java.lang.Object.wait(Object.java:502)
	at com.shigeqiu.demo.BB.bb(BB.java:10)
	at com.shigeqiu.demo.BB.main(BB.java:19)
```

## notify没有获取锁的情况

``` java
public class BB {
    private void bb() {
        this.notify();
        System.out.println("bbbb");
    }

    public static void main(String[] args) {
        new BB().bb();
    }
}
```
异常：非法监视器状态异常
```
Exception in thread "main" java.lang.IllegalMonitorStateException
	at java.lang.Object.notify(Native Method)
	at com.shigeqiu.demo.BB.bb(BB.java:9)
	at com.shigeqiu.demo.BB.main(BB.java:14)
```


## wait和notify执行顺序情况

``` java
public class WaitNotifyOrder {

    public static void main(String[] args) throws InterruptedException {
        MyLock myLock = new MyLock();
        Thread threadA = new Thread(new ThreadA(myLock));
        Thread threadB = new Thread(new ThreadB(myLock));
        threadA.start();
        Thread.sleep(1000);
        threadB.start();
    }

    static class MyLock {
    }


    static class ThreadA implements Runnable {
        private MyLock myLock;

        public ThreadA(MyLock myLock) {
            this.myLock = myLock;
        }
        @Override
        public void run() {
            synchronized (myLock) {
                System.out.println("A before");
                try {
                    myLock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("A after");
            }
        }
    }

    static class ThreadB implements Runnable {
        private MyLock myLock;

        public ThreadB(MyLock myLock) {
            this.myLock = myLock;
        }
        @Override
        public void run() {
            synchronized (myLock) {
                System.out.println('B');
                myLock.notify();
            }
        }
    }

}
```

输出如下：

```
A before
B
A after
```