# Semaphore

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