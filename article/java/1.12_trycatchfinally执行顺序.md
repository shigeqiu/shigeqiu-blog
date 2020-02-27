# try catch finally 执行顺序

<!-- TOC -->

- [try catch finally 执行顺序](#try-catch-finally-执行顺序)
    - [try catch finally 执行顺序结论](#try-catch-finally-执行顺序结论)
    - [案例1](#案例1)
    - [案例2](#案例2)
    - [案例3](#案例3)
    - [包装类型](#包装类型)
    - [案例4](#案例4)
    - [案例5](#案例5)
    - [案例6](#案例6)
    - [总结](#总结)

<!-- /TOC -->

## try catch finally 执行顺序结论

1. 不管有没有出现异常，finally块中代码都会执行；
1. 当try和catch中有return时，finally仍然会执行；
1. finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，不管finally中的代码怎么样，返回的值都不会改变，任然是之前保存的值），所以函数返回值是在finally执行前确定的；
1. finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。

## 案例1

``` java
public class FinallyTest {

    public static void main(String[] args) {
        System.out.print(new FinallyTest().test1());
    }

    int test1(){
        int x = 1;
        try {
            return ++x;
        }finally {
            ++x;
        }
    }
}
```

```
2
```
分析：在try语句中，在执行return语句时，要返回的结果已经准备好了，就在此时，程序转到finally执行了。
在转去之前，try中先把要返回的结果存放到不同于x的局部变量中去，执行完finally之后，在从中取出返回结果，
因此，即使finally中对变量x进行了改变，但是不会影响返回结果。

## 案例2

``` java
public class FinallyTest {

    public static void main(String[] args) {
        System.out.print(new FinallyTest().test2());
    }

    int test2(){
        int i = 1;
        try{
            i++;
            System.out.println("try block, i = "+i);
        }catch(Exception e){
            i++;
            System.out.println("catch block i = "+i);
        }finally{
            i = 10;
            System.out.println("finally block i = "+i);
        }
        return i;
    }
}
```

```
try block, i = 2
finally block i = 10
10
```

没错，会按照顺序执行，先执行try内代码段，没有异常的话进入finally，最后返回。

## 案例3

``` java
public class FinallyTest {

    public static void main(String[] args) {
        System.out.print(new FinallyTest().test3());
    }

    int test3(){
        int i = 1;
        try{
            i++;
            System.out.println("try block, i = "+i);
            return i;
        }catch(Exception e){
            i ++;
            System.out.println("catch block i = "+i);
            return i;
        }finally{
            i = 10;
            System.out.println("finally block i = "+i);
        }
    }
}
```

```
try block, i = 2
finally block i = 10
2
```

分析：代码顺序执行从try到finally，由于finally是无论如何都会执行的，所以try里的语句并不会直接返回。在try语句的return块中，return返回的引用变量并不是try语句外定义的引用变量i,而是系统重新定义了一个局部引用 `i`，这个引用指向了引用 `i` 对应的值，也就是 `2`，即使在finally语句中把引用 `i` 指向了值 `10` ，因为return返回的引用已经不是 `i` ,而是 `i` ,所以引用i的值和try语句中的返回值无关了。

## 包装类型

但是，这只是一部分，如果把 `i` 换成引用类型而不是基本类型呢，来看看输出结果怎样，示例如下：

``` java
public class FinallyTest {
    public static void main(String[] args) {
        System.out.print(new FinallyTest().testWrap());
    }

    List<Object> testWrap(){
        List<Object> list = new ArrayList<>();
        try{
            list.add("try");
            System.out.println("try block");
            return list;
        }catch(Exception e){
            list.add("catch");
            System.out.println("catch block");
            return list;
        }finally{
            list.add("finally");
            System.out.println("finally block ");
        }
    }
}
```

```
try block
finally block
main test i = [try, finally]
```

可以看到，finally里对list集合的操作生效了，这是为什么呢。我们知道基本类型在栈中存储，而对于非基本类型是存储在堆中的，返回的是堆中的地址，因此内容被改变了。


## 案例4

现在我们在finally里加一个return，看看语句是从哪里返回的。

``` java
public class FinallyTest {

    public static void main(String[] args) {
        System.out.print(new FinallyTest().test3());
    }

    int test3(){
        int i = 1;
        try{
            i++;
            System.out.println("try block, i = "+i);
            return i;
        }catch(Exception e){
            i ++;
            System.out.println("catch block i = "+i);
            return i;
        }finally{
            i = 10;
            System.out.println("finally block i = "+i);
            return i;  //HERE
        }
    }
}
```

```
try block, i = 2
finally block i = 10
10
```

可以看到，是从finally语句块中返回的。可见，JVM是忽略了try中的return语句。但IDE中会对finally中加的return有黄色警告提示，这是为什么呢。

## 案例5

在try里加入一行会执行异常的代码，如下：

``` java
public class FinallyTest {

    public static void main(String[] args) {
        System.out.print(new FinallyTest().test3());
    }

    int test3(){
        int i = 1;
        try{
            i++;
            int m = i / 0; // HERE
            System.out.println("try block, i = "+i);
            return i;
        }catch(Exception e){
            i ++;
            System.out.println("catch block i = "+i);
            return i;
        }finally{
            i = 10;
            System.out.println("finally block i = "+i);
            return i;
        }
    }
}
```

```
catch block i = 3
finally block i = 10
10
```

可以看到，因为finally中有return语句，try、catch中的异常被消化掉了，屏蔽了异常的发生，这与初期使用try、catch的初衷是相违背的，因此编译器也会提示警告。

## 案例6

那如果在finally中有异常发生，会对try、catch中的异常有什么影响呢？

``` java
public class FinallyTest {

    public static void main(String[] args) {
        System.out.print(new FinallyTest().test3());
    }

    int test3(){
        int i = 1;
        try{
            i++;
            System.out.println("try block, i = "+i);
            return i;
        }catch(Exception e){
            i ++;
            System.out.println("catch block i = "+i);
            return i;
        }finally{
            i = 10;
            int m = i / 0; // HERE
            System.out.println("finally block i = "+i);
            return i;
        }
    }
}
```

```
try block, i = 2
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at com.shigeqiu.demo.FinallyTest.test3(FinallyTest.java:21)
	at com.shigeqiu.demo.FinallyTest.main(FinallyTest.java:6)
```

这个提示表示的是finally里的异常信息，也就是说一旦finally里发生异常，try、catch里的异常信息即被消化掉了，也达不到异常信息处理的目的。


## 总结

总结以上测试：
1. finally语句总会执行
2. 如果try、catch中有return语句，finally中没有return，那么在finally中修改除包装类型和静态变量、全局变量以外的数据都不会对try、catch中返回的变量有任何的影响（包装类型、静态变量会改变、全局变量）
3. 尽量不要在finally中使用return语句，如果使用的话，会忽略try、catch中的返回语句，也会忽略try、catch中的异常，屏蔽了错误的发生
4. finally中避免再次抛出异常，一旦finally中发生异常，代码执行将会抛出finally中的异常信息，try、catch中的异常将被忽略
所以在实际项目中，finally常常是用来关闭流或者数据库资源的，并不额外做其他操作。