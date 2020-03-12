# wait和notify执行顺序

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