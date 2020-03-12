# 面试题 顺序输出ABC

有三个线程ID分别是A、B、C,请用多线编程实现，在屏幕上循环打印10次ABCABC
请补充以下代码

``` java
public class Test { 
    public static void main(String[] args) { 
        MajusculeABC maj = new MajusculeABC(); 
        Thread t_a = new Thread(new Thread_ABC(maj , 'A')); 
        Thread t_b = new Thread(new Thread_ABC(maj , 'B')); 
        Thread t_c = new Thread(new Thread_ABC(maj , 'C')); 
        t_a.start(); 
        t_b.start(); 
        t_c.start(); 
    } 
} 
class MajusculeABC { 
请补充代码
} 
class Thread_ABC implements Runnable {
请补充代码
}
```


**答案如下：**

``` java
public class ThreadTest {
    public static void main(String[] args) throws InterruptedException {
        MajusculeABC maj = new MajusculeABC();
        Thread t_a = new Thread(new Thread_ABC(maj , 'A'));
        Thread t_b = new Thread(new Thread_ABC(maj , 'B'));
        Thread t_c = new Thread(new Thread_ABC(maj , 'C'));
        t_a.start();
        t_b.start();
        t_c.start();
    }
    private static class MajusculeABC {
        //        请补充代码
        public MajusculeABC() {
        }

        private int a = 1;

        private synchronized void print(int index, char s) throws InterruptedException {
            do {
                if (s == 'A' && a == 1) {
                    a++;
                    System.out.println(index+"---" + s);
                    notifyAll();
                    break;
                } else if (s == 'B' && a == 2) {
                    a++;
                    System.out.println(index+"---" + s);
                    notifyAll();
                    break;
                } else if (s == 'C' && a == 3) {
                    a = 1;
                    System.out.println(index+"---" + s);
                    notifyAll();
                    break;
                } else {
                    wait();
                }

            } while (true);

        }
    }

    private static class Thread_ABC implements Runnable {

        private char s;
        private MajusculeABC majusculeABC;

        //        请补充代码
        public Thread_ABC(MajusculeABC majusculeABC, char s) {
            this.majusculeABC = majusculeABC;
            this.s = s;
        }

        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                try {
                    majusculeABC.print(i, s);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

}
```

输出如下：

```
0---A
0---B
0---C
1---A
1---B
1---C
2---A
2---B
2---C
3---A
3---B
3---C
4---A
4---B
4---C
5---A
5---B
5---C
6---A
6---B
6---C
7---A
7---B
7---C
8---A
8---B
8---C
9---A
9---B
9---C
```