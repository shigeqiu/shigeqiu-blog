# swich case 细节

<!-- TOC -->

- [swich case 细节](#swich-case-细节)
    - [常量表达式](#常量表达式)
        - [错误1](#错误1)
        - [错误2](#错误2)
        - [错误3](#错误3)
        - [错误4](#错误4)
        - [正常](#正常)
        - [case 表达式](#case-表达式)
    - [支持的类型](#支持的类型)
    - [执行顺序](#执行顺序)
        - [案例1](#案例1)
        - [案例2](#案例2)
        - [案例3](#案例3)
    - [结论](#结论)

<!-- /TOC -->

## 常量表达式

### 错误1

``` java

public class SwichCaseTest {

    private static final String A = new String("a");
    private static final String B = new String("b");
    private static final String C = "c";

    public static void main(String[] args) {
        String a = "b";

        switch (a) {
            case A: // A 会被标注红色下划线
                System.out.println(A);
            case B: // B 会被标注红色下划线
                System.out.println(B);
            case C:
                System.out.println(C);
            default:
                System.out.println("default");
        }

    }
}

```
编译错误，位置在case里边的 `A` 和 `B` 变量会被标注红色下划线。错误如下（翻译过来的意思是需要常量字符串表达式）：
```
constant expression required 
```

### 错误2

``` java
public class SwichCaseTest {

    private static final String A = "b";
    private static final String B = "b";
    private static final String C = "c";

    public static void main(String[] args) {
        String a = "b";

        switch (a) {
            case A: // A 会被标注红色下划线
                System.out.println(A);
            case B: // B 会被标注红色下划线
                System.out.println(B);
            case C:
                System.out.println(C);
            default:
                System.out.println("default");
        }

    }
}
```

编译错误，位置在case里边的 `A` 和 `B` 变量会被标注红色下划线。错误如下（翻译过来的意思是 case标签重复）：
```
duplicate label 'b'
```

### 错误3

``` java
 public class SwichCaseTest {

    private static String A = "a";
    private static final String B = "b";
    private static final String C = "c";

    public static void main(String[] args) {
        String a = "b";

        switch (a) {
            case A: // A 会被标注红色下划线
                System.out.println(A);
            case B:
                System.out.println(B);
            case C:
                System.out.println(C);
            default:
                System.out.println("default");
        }

    }
}
```

编译错误，位置在case里边的 `A` 变量会被标注红色下划线。错误如下（翻译过来的意思是需要常量字符串表达式）：
```
constant expression required 
```

### 错误4

``` java
public class SwichCaseTest {

    private static final String A = ma();
    private static final String B = "b";
    private static final String C = "c";

    public static void main(String[] args) {
        String a = "b";

        switch (a) {
            case A: // A 会被标注红色下划线
                System.out.println(A);
            case B:
                System.out.println(B);
            case C:
                System.out.println(C);
            default:
                System.out.println("default");
        }

    }

    private static String ma(){
        return "a";
    }
}
```

编译错误，位置在case里边的 `A` 变量会被标注红色下划线。错误如下（翻译过来的意思是需要常量字符串表达式）：
```
constant expression required 
```

### 正常

``` java
public class SwichCaseTest {

    private static final String A = "a";
    private static final String B = "b";
    private static final String C = "c";

    public static void main(String[] args) {
        String a = "b";

        switch (a) {
            case A:
                System.out.println(A);
            case B:
                System.out.println(B);
            case C:
                System.out.println(C);
            default:
                System.out.println("default");
        }

    }

}
```

运行正常，输出如下：

```
b
c
default
```

### case 表达式

``` java
public class SwichCaseTest {

    public static void main(String[] args) {

        String value = "cc";
        switch (value) {
            case "a":
                System.out.println("a");
            case "b":
                System.out.println("b");
            case "c"+"c":  // HERE
                System.out.println("c");
            default:
                System.out.println("default");
        }

    }

}
```
输出：
```
c
default
```

结论：

case后面只能是常量，可以是运算表达式，但一定要符合正确的类型。不能是变量，即便变量在之前进行了赋值，JVM依然会报错。

## 支持的类型

- 基本数据类型：byte, short, char, int
- 包装数据类型：Byte, Short, Character, Integer
- 枚举类型：Enum
- 字符串类型：String（JDK1.7之后 开始支持）

## 执行顺序

### 案例1

``` java
public class SwichCaseTest {

    public static void main(String[] args) {

        String value = "v";
        switch (value) {
            case "a":
                System.out.println("a");
            case "b":
                System.out.println("b");
            case "c":
                System.out.println("c");
            case "d":
                System.out.println("d");
            case "e":
                System.out.println("e");
            default:
                System.out.println("default");
        }

    }

}
```

结果：

```
default
```

结论：

当每一个case中 **存在或者不存在** break时，进行顺序匹配，若未匹配成功则执行默认的 `default` 代码块。

### 案例2

``` java
public class SwichCaseTest {

    public static void main(String[] args) {

        String value = "c";
        switch (value) {
            case "a":
                System.out.println("a");
            case "b":
                System.out.println("b");
            case "c":
                System.out.println("c");
            case "d":
                System.out.println("d");
            case "e":
                System.out.println("e");
            default:
                System.out.println("default");
        }

    }

}
```

输出：

```
c
d
e
default
```
结论：

当每一个case都不存在break时，匹配成功后，从当前case开始，依次执行后续所有case代码块。

### 案例3

``` java
public class SwichCaseTest {

    public static void main(String[] args) {

        String value = "c";
        switch (value) {
            case "a":
                System.out.println("a");
            case "b":
                System.out.println("b");
            case "c":
                System.out.println("c");
            case "d":
                System.out.println("d");
                break;  //HERE
            case "e":
                System.out.println("e");
            default:
                System.out.println("default");
        }

    }

}
```

输出：

```
c
d
```

结论：

若当前匹配成功的case不存在break，则从当前case开始，依次执行后续case代码块，直到遇到break，跳出判断。

## 结论

因此switch case执行时，一定会先进行匹配，匹配成功返回当前case的值，再根据是否有break，判断是否继续输出，或是跳出判断。

还需注意的是case后面只能是常量，可以是运算表达式，但一定要符合正确的类型。不能是变量，即便变量在之前进行了赋值，JVM依然会报错。