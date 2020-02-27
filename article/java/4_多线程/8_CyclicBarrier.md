# CyclicBarrier



栅栏类似于闭锁，它能阻塞一组线程直到某个事件的发生。栅栏与闭锁的关键区别在于，所有的线程必须同时到达栅栏位置，才能继续执行。闭锁用于等待事件，而栅栏用于等待其他线程。

CyclicBarrier可以使一定数量的线程反复地在栅栏位置处汇集。当线程到达栅栏位置时将调用await方法，这个方法将阻塞直到所有线程都到达栅栏位置。如果所有线程都到达栅栏位置，那么栅栏将打开，此时所有的线程都将被释放，而栅栏将被重置以便下次使用。

``` java
public class CyclicBarrierDemo {

    public static void main(String[] args) {
        int count = 5;
        CyclicBarrier cyclicBarrier = new CyclicBarrier(count);
        for (int i = 0; i < count; i++) {
            new Tangerine(String.valueOf(i),cyclicBarrier).start();
        }

        System.out.printf("main线程等待\n");
        System.out.printf("main线程运行结束\n");
        System.out.println(cyclicBarrier.getParties());
    }

    static class Tangerine extends Thread {
        final CyclicBarrier cyclicBarrier;
        public Tangerine(String name,CyclicBarrier cyclicBarrier) {
            super(name);
            this.cyclicBarrier = cyclicBarrier;
        }

        @Override
        public void run() {
            System.out.printf("线程%s在运行\n", this.getName());
            try {
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
            System.out.printf("线程%s在继续运行\n", this.getName());
        }
    }
}
```