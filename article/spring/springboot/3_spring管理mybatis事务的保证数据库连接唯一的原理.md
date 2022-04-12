# spring管理mybatis事务的保证数据库连接唯一的原理



- [spring管理mybatis事务的保证数据库连接唯一的原理](#spring管理mybatis事务的保证数据库连接唯一的原理)
  - [原始的JDBC](#原始的jdbc)
  - [用AOP技术保持当前的Connection](#用aop技术保持当前的connection)
  - [Service层和Dao层共享Connection](#service层和dao层共享connection)
  - [事务为什么要切在Service层的理由](#事务为什么要切在service层的理由)


> 参考；
> - https://www.cnblogs.com/guyaoblog/p/11421978.html
> - https://blog.csdn.net/BuquTianya/article/details/78946473



## 原始的JDBC


在开始之前，先让我们回忆一下数据库较原始的JDBC是怎么管理事务的：

``` java
//仅做演示，代码不完整，不完全规范
try {
    con.setAutoCommit(false);
    statement1 = con.prepareStatement(sql);
    statement1.executeUpdate();
    statement2 = con.prepareStatement(sql1);
    statement2.executeUpdate();
    con.commit();
} catch (SQLException e) {
    try {
        con.rollback();
    } catch (SQLException e1) {
    }
}
```

可以很明显的看到，JDBC框架下的事务控制是由connection完成的。因为不论是MyBatis还是MyBatis-Spring都是在JDBC框架基础上的高层框架，所以他们的原理仍然应该是一致的，也就是说想控制事务，必须要控制Connection。

我们常说事务要切在Service层，所以连接需要在整个Service请求中都是同一个，不能变。


## 用AOP技术保持当前的Connection

Spring的事务管理就是使用AOP技术，通过对Service层设置切面，注入事务管理的逻辑。



Spring的事务管理切面配置采用了声明式事务，最常用的两种方法是 `tx:Advice` 和 `tx:annotation-driven` 两种方式。两种方式的配置文件解析器分别是: 

- `org.springframework.transaction.config.TxAdviceBeanDefinitionParser`
- `org.springframework.transaction.config.AnnotationDrivenBeanDefinitionParser`

细看其中的代码和配置内容，就会发现，不论哪种方式都会创建包含事务处理功能的动态代理。代理关联的切面（Advice）类是 `TransactionInterceptor` 。


``` java
protected Object invokeWithinTransaction(Method method, Class<?> targetClass, final InvocationCallback invocation)
        throws Throwable {
    //......
    if (txAttr == null || !(tm instanceof CallbackPreferringPlatformTransactionManager)) {
        // ----- 1.开启事务
        TransactionInfo txInfo = createTransactionIfNecessary(tm, txAttr, joinpointIdentification);
        Object retVal = null;
        try {
            //...2.执行被代理的请求
            retVal = invocation.proceedWithInvocation();
        }
        catch (Throwable ex) {
            //...3.异常回滚
            completeTransactionAfterThrowing(txInfo, ex);
            throw ex;
        }
        finally {
            cleanupTransactionInfo(txInfo);
        }
        //...4.提交事务
        commitTransactionAfterReturning(txInfo);
        return retVal;
    }
    //......
}
```


上边的代码里是不是没有看到TransactionManger和Connection？那么这两个东西的作用在哪里呢？

TransactionManger是上述TransactionInterceptor的一个属性（不严的说），主要作用是用来创建connection，创建之后的Connection会被保存在TransactionInfo里面。

TransactionInfo在上述代码片段中被后续传递给事务提交和事务回滚的代码。

## Service层和Dao层共享Connection


我们都知道事务要切在Service层，也就是说上一节的切面只是在Service层有效，那么Dao层怎么获取到Connection连接呢？

如果Service层和Dao层的连接不是一个连接那么回滚和提交操作就等同于无效了！

这里只用MyBatis来说明，其他的ORM框架实现原理基本也是一样的。

要明白这一点需要先弄明白MyBatis本身的事务管理机制，可以参考唯一浩哥的博文 [MyBatis源码解析（三）——Transaction事务模块](https://www.cnblogs.com/V1haoge/p/6634151.html) 。MyBatis提供了两种事务管理机制一种是自己内部用的JDBC模式，一种是支持代理给外部控制的MANAGED模式。

第二种模式下会把事务的交给外部控制，外部只需要提供一个实现了 `org.apache.ibatis.transaction.Transaction` 接口的控制类即可。一起来看一下Transaction需要提供哪些方法：


``` java
public interface Transaction {
  Connection getConnection() throws SQLException;
  void commit() throws SQLException;
  void rollback() throws SQLException;
  void close() throws SQLException;
}
```

注意里边的getConnection方法，也就是说MyBatis的连接也是交给外部来获取的！！那么只需要想办法把Service层的Connection存起来，然后让自己实现Transaction获取到即可。


Spring采用的是ThreadLocal本地线程变量的技术来做到的，我们可以看下mybatis-spring的 `org.mybatis.spring.transaction.SpringManagedTransaction` 中getConnection的实现就明白了：

``` java
  public Connection getConnection() throws SQLException {
    if (this.connection == null) {
      openConnection();
    }
    return this.connection;
  }

  private void openConnection() throws SQLException {
    this.connection = DataSourceUtils.getConnection(this.dataSource);//...1.关键点在这里！！
    this.autoCommit = this.connection.getAutoCommit();
    this.isConnectionTransactional = DataSourceUtils.isConnectionTransactional(this.connection, this.dataSource);
    if (LOGGER.isDebugEnabled()) {
      LOGGER.debug(
          "JDBC Connection ["
              + this.connection
              + "] will"
              + (this.isConnectionTransactional ? " " : " not ")
              + "be managed by Spring");
    }
  }
```

其中一行就会从ThreadLocal中拿到Connection对象，如下所示

``` java
this.connection = DataSourceUtils.getConnection(this.dataSource);
```

## 事务为什么要切在Service层的理由

对于这个常识，有一点个人的理解：

事务的ACID要求事务要有原子性，也就是一个事务里边的多项DB操作要同时成功，同时失败，成功一半的情况是不允许的。也就是说，一般需要事务的时候，都是包含多个功能单元的。那么我们都放在一个Dao里面就显得不那么职能分明，也就是不那么符合设计原则的单一职责原则。