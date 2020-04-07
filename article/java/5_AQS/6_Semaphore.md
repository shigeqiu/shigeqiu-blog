# Semaphore

> https://www.bbsmax.com/A/MyJx9Vma5n/


> A counting semaphore. Conceptually, a semaphore maintains a set of permits. Each acquire blocks if necessary until a permit is available, and then takes it. Each release adds a permit, potentially releasing a blocking acquirer. However, no actual permit objects are used; the Semaphore just keeps a count of the number available and acts accordingly.

- Semaphore是一个计数信号量。
- 从概念上将，Semaphore包含一组许可证。
- 如果有需要的话，每个acquire()方法都会阻塞，直到获取一个可用的许可证。
- 每个release()方法都会释放持有许可证的线程，并且归还Semaphore一个可用的许可证。
- 然而，实际上并没有真实的许可证对象供线程使用，Semaphore只是对可用的数量进行管理维护。

``` java

public class SemaphoreTest {

    public static void main(String[] args) {
        int count = 5;

        Semaphore semaphore = new Semaphore(count);
        for (int i = 0; i < 10; i++) {
            new Tangerine(String.valueOf(i), semaphore).start();
        }
        System.out.printf("main线程等待\n");
        System.out.printf("main线程运行结束\n");
    }

    static class Tangerine extends Thread {
        final Semaphore semaphore;
        public Tangerine(String name,Semaphore semaphore) {
            super(name);
            this.semaphore = semaphore;
        }

        @Override
        public void run() {
            try {
                semaphore.acquire();
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.printf("线程%s在运行\n", this.getName());
            semaphore.release();
        }
    }
}
```

## 应用场景

- 20个人去银行存款，但是该银行只有两个办公柜台，有空位则上去存钱，没有空位则只能去排队等待
- 


## 面试题思考

在很多情况下，可能有多个线程需要访问数目很少的资源。假想在服务器上运行着若干个回答客户端请求的线程。这些线程需要连接到同一数据库，但任一时刻只能获得一定数目的数据库连接。你要怎样才能够有效地将这些固定数目的数据库连接分配给大量的线程？


答：

1. 给方法加同步锁，保证同一时刻只能有一个人去调用此方法，其他所有线程排队等待，但是此种情况下即使你的数据库链接有10个，也始终只有一个处于使用状态。这样将会大大的浪费系统资源，而且系统的运行效率非常的低下。
2. 另外一种方法当然是使用信号量，通过信号量许可与数据库可用连接数相同的数目，将大大的提高效率和性能。