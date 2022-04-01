# Optional 

## 静态方法

| 方法  |  说明 |
|---|---|
| Optional.of(T)  | 创建指定引用的Optional实例，若为null则立即报错 |
| Optional.absent()  | 创建引用null的Optional实例  |
| Optional.fromNullable(T)  | 创建指定引用的Optional实例，可以为null，也可以不为null  |

## 实例方法

| 方法  |  说明 |
|---|---|
| boolean isPresent()  | 如果Optional包含非null的引用，返回true |
| T get()  | 返回Optional所包含的引用，若引用缺失，则抛出java.lang.IllegalStateException |
| T or(T)  | 返回Optional所包含的引用，若引用缺失，返回指定的值  |
| T orNull()  | 返回Optional所包含的引用，若引用缺失，返回null  |
| Set<T> asSet()  | 返回Optional所包含引用的单例不可变集，如果引用存在，返回一个只有单一元素的集合，如果引用缺失，返回一个空集合。  |