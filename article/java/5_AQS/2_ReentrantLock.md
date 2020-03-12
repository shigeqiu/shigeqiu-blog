# ReentrantLock（显示锁）

支持以下功能：

1. 支持公平和非公平的获取锁的方式。
1. 支持可重入。

公平锁与非公平锁

1. 公平锁：加锁前先查看是否有排队等待的线程，有的话优先处理排在前面的线程，先来先得。
2. 非公平锁：线程加锁时直接尝试获取锁，获取不到就自动到队尾等待。


## lock(),unlock()方法说明

该demo模拟电影院的售票情况,tickets总票数。开启了10个窗口售票，售完为止

``` java
public class ReentrantLockDemo01 implements Runnable {

    private Lock lock = new ReentrantLock();

    private int tickets = 200;

    @Override
    public void run() {
        while (true) {
            lock.lock(); // 获取锁
            try {
                if (tickets > 0) {
                    TimeUnit.MILLISECONDS.sleep(100);
                    System.out.println(Thread.currentThread().getName() + " " + tickets--);
                } else {
                    break;
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock(); // 释放所
            }
        }
    }

    public static void main(String[] args) {
        ReentrantLockDemo01 reentrantLockDemo = new ReentrantLockDemo01();
        for (int i = 0; i < 10; i++) {
            Thread thread = new Thread(reentrantLockDemo, "thread" + i);
            thread.start();
        }
    }
}
```

## newCondition()方法

Condition的作用是对锁进行更精确的控制。Condition中的await()方法相当于Object的wait()方法，Condition中的signal()方法相当于Object的notify()方法，Condition中的signalAll()相当于Object的notifyAll()方法。不同的是，Object中的wait(),notify(),notifyAll()方法是和”同步锁”(synchronized关键字)捆绑使用的；而Condition是需要与”互斥锁”/”共享锁”捆绑使用的。

``` java
/**
 * 生产者消费者
 */
public class ProducerConsumerTest {

    private Lock lock = new ReentrantLock();

    private Condition addCondition = lock.newCondition();

    private Condition removeCondition = lock.newCondition();

    private LinkedList<Integer> resources = new LinkedList<>();

    private int maxSize;

    public ProducerConsumerTest(int maxSize) {
        this.maxSize = maxSize;
    }


    public class Producer implements Runnable {

        private int proSize;

        private Producer(int proSize) {
            this.proSize = proSize;
        }

        @Override
        public void run() {
            lock.lock();
            try {
                for (int i = 1; i < proSize; i++) {
                    while (resources.size() >= maxSize) {
                        System.out.println("当前仓库已满，等待消费...");
                        try {
                            addCondition.await();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    System.out.println("已经生产产品数: " + i + "\t现仓储量总量:" + resources.size());
                    resources.add(i);
                    removeCondition.signal();
                }
            } finally {
                lock.unlock();
            }

        }
    }

    public class Consumer implements Runnable {

        @Override
        public void run() {
            String threadName = Thread.currentThread().getName();
            while (true) {
                lock.lock();
                try {
                    while (resources.size() <= 0) {
                        System.out.println(threadName + " 当前仓库没有产品，请稍等...");
                        try {
                            // 进入阻塞状态
                            removeCondition.await();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                    // 消费数据
                    int size = resources.size();
                    for (int i = 0; i < size; i++) {
                        Integer remove = resources.remove();
                        System.out.println(threadName + " 当前消费产品编号为：" + remove);
                    }
                    // 唤醒生产者
                    addCondition.signal();
                } finally {
                    lock.unlock();
                }
            }

        }
    }

    public static void main(String[] args) throws InterruptedException {
        ProducerConsumerTest producerConsumerTest = new ProducerConsumerTest(10);
        Producer producer = producerConsumerTest.new Producer(100);
        Consumer consumer = producerConsumerTest.new Consumer();
        final Thread producerThread = new Thread(producer, "producer");
        final Thread consumerThread = new Thread(consumer, "consumer");
        producerThread.start();
        TimeUnit.SECONDS.sleep(2);
        consumerThread.start();
    }
}
```