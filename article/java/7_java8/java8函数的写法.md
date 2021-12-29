# Java8 函数的写法

链式写法

``` java
@Test
public void testConsumerLine() {
    ((Consumer<String>) (s) -> System.out.println("1" + s))
            .andThen(s -> System.out.println("2" + s))
            .andThen(s -> System.out.println("3" + s))
            .accept("a");
}
```

输出

```
1a
2a
3a
```