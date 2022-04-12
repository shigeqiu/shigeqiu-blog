# Feign

## 超时时间

因为ribbon的重试机制和Feign的重试机制有冲突，所以源码中默认关闭Feign的重试机制，源码如下:

``` java
public interface Retryer extends Cloneable {
 /**
   * Implementation that never retries request. It propagates the RetryableException.
   */
  Retryer NEVER_RETRY = new Retryer() {

    @Override
    public void continueOrPropagate(RetryableException e) {
      throw e;
    }

    @Override
    public Retryer clone() {
      return this;
    }
  };
}
```

要开启Feign的重试机制如下：（Feign默认重试五次 源码中有）:

``` java

@Bean
Retryer feignRetryer() {
        return  new Retryer.Default();
}

public interface Retryer extends Cloneable {
    class Default implements Retryer {
    }
}
```