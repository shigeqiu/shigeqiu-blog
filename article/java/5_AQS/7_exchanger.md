# Exchanger交换者

Exchanger（交换者）是一个用于线程间协作的工具类。Exchanger用于进行线程间的数据交换。它提供一个同步点，在这个同步点两个线程可以交换彼此的数据。这两个线程通过exchange方法交换数据， 如果第一个线程先执行exchange方法，它会一直等待第二个线程也执行exchange，当两个线程都到达同步点时，这两个线程就可以交换数据，将本线程生产出来的数据传递给对方。因此使用Exchanger的重点是成对的线程使用exchange()方法，当有一对线程达到了同步点，就会进行交换数据。因此该工具类的线程对象是成对的。

两个方法：

- `String exchange(V x)`:用于交换，启动交换并等待另一个线程调用exchange，返回交换后得到的值。
- `String exchange(V x,long timeout,TimeUnit unit)`：用于交换，启动交换并等待另一个线程调用exchange，并且设置最大等待时间，当等待时间超过timeout便停止等待。

## 案例

``` java
public class ExchangeTest {

    public static void main(String[] args) throws InterruptedException {
        Exchanger<Integer> exchanger = new Exchanger();

        Tom tom = new Tom(exchanger);
        Jack jack = new Jack(exchanger);

        tom.start();
        jack.start();

        tom.join();
        jack.join();
    }

    static class Tom extends Thread {
        private Exchanger<Integer> exchanger;

        Tom(Exchanger<Integer> exchanger){
            this.exchanger = exchanger;
        }

        @Override
        public void run() {
            for (int i = 21; i < 26; i++) {
                try {
                    TimeUnit.SECONDS.sleep(1);
                    System.out.println("Tom  交换前： " + i);
                    int after = exchanger.exchange(i);
                    System.out.println("Tom  交换后： " + after);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
        }
    }

    static class Jack extends Thread {
        private Exchanger<Integer> exchanger;

        Jack(Exchanger<Integer> exchanger){
            this.exchanger = exchanger;
        }

        @Override
        public void run() {
            for (int i = 61; i < 66; i++) {
                try {
                    TimeUnit.SECONDS.sleep(1);
                    System.out.println("Jack 交换前： " + i);
                    int after = exchanger.exchange(i);
                    System.out.println("Jack 交换后： " + after);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
        }
    }
}
```

输出：
```
Tom  交换前： 21
Jack 交换前： 61
Tom  交换后： 61
Jack 交换后： 21
Tom  交换前： 22
Jack 交换前： 62
Jack 交换后： 22
Tom  交换后： 62
Jack 交换前： 63
Tom  交换前： 23
Tom  交换后： 63
Jack 交换后： 23
Tom  交换前： 24
Jack 交换前： 64
Tom  交换后： 64
Jack 交换后： 24
Jack 交换前： 65
Tom  交换前： 25
Jack 交换后： 25
Tom  交换后： 65
```