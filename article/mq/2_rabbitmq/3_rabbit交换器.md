# RabbitMQ 交换器

<!-- TOC -->

- [RabbitMQ 交换器](#rabbitmq-交换器)
  - [direct](#direct)
  - [fanout](#fanout)
  - [topic](#topic)
  - [headers](#headers)

<!-- /TOC -->


- 官方例子 http://www.rabbitmq.com/getstarted.html

## direct

![](img/direct_1.jpg)

如上图所示，两个队列绑定到了direct交换器上，第一个队列绑定的 `binding key` 为 `orange` ，第二个队列有两个绑定，分别是 `black` 和 `green` 。


![](img/direct_2.jpg)

如上图所示，用相同的 `binding key` 绑定到多个队列上是完全OK的。


![](img/direct_3.jpg)

上图为日志系统所使用的direct交换器场景。

direct交换器的路由算法很简单，一条消息将会进入 `binding key` （队列的绑定） 和消息的 `routing key` 完全匹配的队列。

如果路由键完全匹配的话，消息就被投递到对应的队列。

## fanout

![](img/fanout_1.jpg)


消息通信模式：当你发送一条消息到 `fanout` 交换器时，它会把消息投递给 **所有** 附加在此交换器上的队列。这允许你对单条消息做不同方式的反应。
 
发送时需要提供 `routing_key` ，但是对于 `fanout ` 交换器，它的值将被忽略。

## topic

![](img/topic_1.jpg)

这类交换器允许你实现有趣的消息通信场景，它使得来自不同源头的消息能够到达同一个队列。

- 单个 `“.”` 把路由键分为几部分
- `“*”` 匹配特定位置的任意文本。
- 为了实现匹配所有规则，你可以实用 `“#”` 字符

## headers

header交换器和direct交换器完全一致，但性能会差很多。因此不太实用，几乎再也用不到了。
