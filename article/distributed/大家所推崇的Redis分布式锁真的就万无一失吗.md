# 大家所推崇的Redis分布式锁真的就万无一失吗

> 原创： 朱小厮  公众号：朱小厮的博客

在单实例JVM中，常见的处理并发问题的方法有很多，比如synchronized关键字进行访问控制、volatile关键字、ReentrantLock等常用方法。但是在分布式环境中，上述方法却不能在跨JVM场景中用于处理并发问题，当业务场景需要对分布式环境中的并发问题进行处理时，需要使用分布式锁来实现。

分布式锁，是指在分布式的部署环境下，通过锁机制来让多客户端互斥的对共享资源进行访问。

目前比较常见的分布式锁实现方案有以下几种：

1. 基于数据库，如MySQL
1. 基于缓存，如Redis
1. 基于Zookeeper、etcd等。

在上一篇《基于数据库实现分布式锁》中介绍了如何基于数据库实现分布式锁，这里介绍一下如何使用缓存（Redis）实现分布式锁。

使用Redis实现分布式锁最简单的方案是使用命令SETNX。SETNX（SET if Not eXist）的使用方式为：SETNX key value，只在键key不存在的情况下，将键key的值设置为value，若键key存在，则SETNX不做任何动作。SETNX在设置成功时返回，设置失败时返回0。当要获取锁时，直接使用SETNX获取锁，当要释放锁时，使用DEL命令删除掉对应的键key即可。

上面这种方案有一个致命问题，就是某个线程在获取锁之后由于某些异常因素（比如宕机）而不能正常的执行解锁操作，那么这个锁就永远释放不掉了。为此，我们可以为这个锁加上一个超时时间。第一时间我们会联想到Redis的EXPIRE命令（EXPIRE key seconds）。但是这里我们不能使用EXPIRE来实现分布式锁，因为它与SETNX一起是两个操作，在这两个操作之间可能会发生异常，从而还是达不到预期的结果，示例如下：

```
// STEP 1
SETNX key value
// 若在这里（STEP1和STEP2之间）程序突然崩溃，则无法设置过期时间，将有可能无法释放锁
// STEP 2
EXPIRE key expireTime
```

对此，正确的姿势应该是使用 `“SET key value [EX seconds] [PX milliseconds] [NX|XX]”` 这个命令。

从 Redis 2.6.12 版本开始， SET 命令的行为可以通过一系列参数来修改：

- `EX seconds` ： 将键的过期时间设置为 seconds 秒。 执行 `SET key value EX seconds` 的效果等同于执行 `SETEX key seconds value` 。
- `PX milliseconds` ： 将键的过期时间设置为 milliseconds 毫秒。 执行 `SET key value PX milliseconds` 的效果等同于执行 `PSETEX key milliseconds value `。
- `NX` ： 只在键不存在时， 才对键进行设置操作。 执行 SET key value NX 的效果等同于执行 `SETNX key value `。
- `XX` ： 只在键已经存在时， 才对键进行设置操作。

举例，我们需要创建一个分布式锁，并且设置过期时间为10s，那么可以执行以下命令：

```
SET lockKey lockValue EX 10 NX
或者
SET lockKey lockValue PX 10000 NX
```

> **注意EX和PX不能同时使用，否则会报错：ERR syntax error。**

解锁的时候还是使用DEL命令来解锁。

修改之后的方案看上去很完美，但实际上还是会有问题。试想一下，某线程A获取了锁并且设置了过期时间为10s，然后在执行业务逻辑的时候耗费了15s，此时线程A获取的锁早已被Redis的过期机制自动释放了。在线程A获取锁并经过10s之后，改锁可能已经被其它线程获取到了。当线程A执行完业务逻辑准备解锁（DEL key）的时候，有可能删除掉的是其它线程已经获取到的锁。

所以最好的方式是在解锁时判断锁是否是自己的。我们可以在设置key的时候将value设置为一个唯一值uniqueValue（可以是随机值、UUID、或者机器号+线程号的组合、签名等）。当解锁时，也就是删除key的时候先判断一下key对应的value是否等于先前设置的值，如果相等才能删除key，伪代码示例如下：

```
if uniqueKey == GET(key) {
    DEL key
}
```

这里我们一眼就可以看出问题来：GET和DEL是两个分开的操作，在GET执行之后且在DEL执行之前的间隙是可能会发生异常的。如果我们只要保证解锁的代码是原子性的就能解决问题了。这里我们引入了一种新的方式，就是Lua脚本，示例如下：

``` lua
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

其中`ARGV[1]`表示设置key时指定的唯一值。由于**Lua脚本的原子性**，在Redis执行该脚本的过程中，其他客户端的命令都需要等待该Lua脚本执行完才能执行。

下面我们使用Jedis来演示一下获取锁和解锁的实现，具体如下：

``` java
public boolean lock(String lockKey, String uniqueValue, int seconds){
    SetParams params = new SetParams();
    params.nx().ex(seconds);
    String result = jedis.set(lockKey, uniqueValue, params);
    if ("OK".equals(result)) {
        return true;
    }
    return false;
}

public boolean unlock(String lockKey, String uniqueValue){
    String script = "if redis.call('get', KEYS[1]) == ARGV[1] " +
            "then return redis.call('del', KEYS[1]) else return 0 end";
    Object result = jedis.eval(script, 
            Collections.singletonList(lockKey), 
            Collections.singletonList(uniqueValue));
    if (result.equals(1)) {
        return true;
    }
    return false;
}
```

如此就万无一失了吗?显然不是！表面来看，这个方法似乎很管用，但是这里存在一个问题：在我们的系统架构里存在一个单点故障，如果Redis的master节点宕机了怎么办呢？有人可能会说：加一个slave节点！在master宕机时用slave就行了！

但是其实这个方案明显是不可行的，因为Redis的复制是异步的。举例来说：

- 线程A在master节点拿到了锁。
- master节点在把A创建的key写入slave之前宕机了。
- slave变成了master节点。
- 线程B也得到了和A还持有的相同的锁。（因为原来的slave里面还没有A持有锁的信息）

当然，在某些场景下这个方案没有什么问题，比如业务模型允许同时持有锁的情况，那么使用这种方案也未尝不可。

举例说明，某个服务有2个服务实例：A和B，初始情况下A获取了锁然后对资源进行操作（可以假设这个操作很耗费资源），B没有获取到锁而不执行任何操作，此时B可以看做是A的热备。当A出现异常时，B可以“转正”。当锁出现异常时，比如Redis master宕机，那么B可能会同时持有锁并且对资源进行操作，如果操作的结果是幂等的（或者其它情况），那么也可以使用这种方案。这里引入分布式锁可以让服务在正常情况下避免重复计算而造成资源的浪费。

为了应对这种情况，antriez提出了Redlock算法。Redlock算法的主要思想是：假设我们有N个Redis master节点，这些节点都是完全独立的，我们可以运用前面的方案来对前面单个的Redis master节点来获取锁和解锁，如果我们总体上能在合理的范围内或者N/2+1个锁，那么我们就可以认为成功获得了锁，反之则没有获取锁（可类比Quorum模型）。虽然Redlock的原理很好理解，但是其内部的实现细节很是复杂，要考虑很多因素，具体内容可以参考：https://redis.io/topics/distlock 。有关Redlock的具体使用方式可以参考我之前转载的两篇文章《Redis分布式锁最牛逼的实现》和《Redission实现Redis分布式锁的N种姿势》。

Redlock算法也并非是“银弹”，他除了条件有点苛刻外，其算法本身也被质疑。关于Redis分布式锁的安全性问题，在分布式系统专家Martin Kleppmann和Redis的作者antirez之间就发生过一场争论。这场争论的内容大致如下：

Martin Kleppmann发表了一篇blog，名字叫 《How to do distributed locking》 ，地址为：https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html 。Martin在这篇文章中谈及了分布式系统的很多基础性的问题（特别是分布式计算的异步模型），对分布式系统的从业者来说非常值得一读。

Martin的那篇文章是在2016-02-08这一天发表的，但据Martin说，他在公开发表文章的一星期之前就把草稿发给了antirez进行review，而且他们之间通过email进行了讨论。不知道Martin有没有意料到，antirez对于此事的反应很快，就在Martin的文章发表出来的第二天，antirez就在他的博客上贴出了他对于此事的反驳文章，名字叫 《Is Redlock safe?》，地址为：http://antirez.com/news/101 。

这是高手之间的过招。antirez这篇文章也条例非常清晰，并且中间涉及到大量的细节。antirez认为，Martin的文章对于Redlock的批评可以概括为两个方面（与Martin文章的前后两部分对应）：

- 带有自动过期功能的分布式锁，必须提供某种fencing机制来保证对共享资源的真正的互斥保护。Redlock提供不了这样一种机制。
- Redlock构建在一个不够安全的系统模型之上。它对于系统的记时假设(timing assumption)有比较强的要求，而这些要求在现实的系统中是无法保证的。

antirez对这两方面分别进行了反驳。

首先，关于fencing机制。antirez对于Martin的这种论证方式提出了质疑：既然在锁失效的情况下已经存在一种fencing机制能继续保持资源的互斥访问了，那为什么还要使用一个分布式锁并且还要求它提供那么强的安全性保证呢？即使退一步讲，Redlock虽然提供不了Martin所讲的递增的fencing token，但利用Redlock产生的随机字符串(my_random_value)可以达到同样的效果。这个随机字符串虽然不是递增的，但却是唯一的，可以称之为unique token。

然后，antirez的反驳就集中在第二个方面上：关于算法在记时(timing)方面的模型假设。在我们前面分析Martin的文章时也提到过，Martin认为Redlock会失效的情况主要有三种：1. 时钟发生跳跃；2. 长时间的GC pause；3. 长时间的网络延迟。

antirez肯定意识到了这三种情况对Redlock最致命的其实是第一点：时钟发生跳跃。这种情况一旦发生，Redlock是没法正常工作的。而对于后两种情况来说，Redlock在当初设计的时候已经考虑到了，对它们引起的后果有一定的免疫力。所以，antirez接下来集中精力来说明通过恰当的运维，完全可以避免时钟发生大的跳动，而Redlock对于时钟的要求在现实系统中是完全可以满足的。

神仙打架，我们站旁边看看就好。抛开这个层面而言，在理解Redlock算法时要理解“各个节点完全独立”这个概念。Redis本身有几种部署模式：单机模式、主从模式、哨兵模式、集群模式。比如采用集群模式部署，如果需要5个节点，那么就需要部署5个Redis Cluster集群。很显然，这种要求每个master节点都独立的Redlock算法条件有点苛刻，使用它所需要耗费的资源比较多，而且对每个节点都请求一次锁所带来的额外开销也不可忽视。除非有实实在在的业务应用需求，或者有资源可以复用。

使用Redis分布式锁并不能做到万无一失。一般而言，Redis分布式锁的优势在于性能，而如果要考虑到可靠性，那么Zookeeper、etcd这类的组件会比Redis要高。当然，在合适的环境下使用基于数据库实现的分布式锁会更合适，参考《基于数据库实现分布式锁》。

不过就以可靠性而言，没有任何组件是完全可靠的，程序员的价值不仅仅在于表象地如何灵活运用这些组件，而在于如何基于这些不可靠的组件构建一个可靠的系统。

还是那句老话，选择何种方案，合适最重要。