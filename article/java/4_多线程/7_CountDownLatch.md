# CountDownLatch(闭锁)

闭锁是一次性对象，一旦进入终止状态，就不能被重置。

``` java
public class CountDownLatchDemo {

    public static void main(String[] args) throws InterruptedException {
        int count = 5;
        CountDownLatch countDownLatch = new CountDownLatch(count);
        for (int i = 0; i < count; i++) {
            new Tangerine(String.valueOf(i),countDownLatch).start();
        }
        System.out.printf("main线程等待\n");
        countDownLatch.await();
        System.out.printf("main线程运行结束");
    }

    static class Tangerine extends Thread {
        final CountDownLatch countDownLatch;
        public Tangerine(String name,CountDownLatch countDownLatch) {
            super(name);
            this.countDownLatch = countDownLatch;
        }

        @Override
        public void run() {
            System.out.printf("线程%s在运行\n", this.getName());
            countDownLatch.countDown();
        }
    }
}
```

- `await()`  等待，直到计数器中的值减为0。
- `await(long timeout, TimeUnit unit)` 可以自己设置超时时间，一旦超过这个时间，await线程被唤醒，如果返回true，说明计数器为0，否则，不为0。
- `countDown()` 使得计数器的值减1。
- `getCount()` 得到当前计数器的值。