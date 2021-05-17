## 一．	Rabbitmq的工作模式
### （一）	Work queues工作队列模式
&nbsp;  &nbsp;  &nbsp;  &nbsp;与简单模式不同，工作队列模式多了一个或者一些消费端，多个消费端共同消费同一个队列中的消息。这种模式常常应用于任务过多的情况，可以提高任务处理的速度。消费者之间对于同一个消息的关系是竞争的关系。
<br>



### （二）	Pub/Sub 订阅模式
#### 1.	流程介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp;订阅模式中，多了交换机的转发过程。生产者将消息发送给交换机，交换机决定如何处理这些消息，如交给所有的队列，或交给特定的队列，或是丢弃这些消息，这些操作取决于 交换机的类型。队列得到这些消息后，消费者监听队列并得到消息。
<br>



#### 2.	交换机类型
##### （1）	Fanout
&nbsp;  &nbsp;  &nbsp;  &nbsp;广播，将消息交给所有绑定到交换机的队列。
<br>



##### （2）	Direct
&nbsp;  &nbsp;  &nbsp;  &nbsp;定向，将消息交给符合指定Routing key的队列。
<br>



##### （3）	Topic
&nbsp;  &nbsp;  &nbsp;  &nbsp;通配符，将消息交给符合Routing pattern（路由模式）的队列。

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，交换机没有存储能力，只负责转发消息，因此若没有队列和交换机绑定或没有符合路由规则的队列时，消息会丢失。
<br>



#### 3.	Fanout模式实例
##### （1）	创建生产者类
&nbsp;  &nbsp;  &nbsp;  &nbsp;生产者类的流程与之前差不多，多了创建交换机，绑定队列和交换机的流程。
<br>



###### a)	创建交换机
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建交换机是使用通道的exchangeDeclare方法进行创建，exchangeDeclare方法的声明如下：

```java
exchangeDeclare(String exchange, BuiltinExchangeType type, boolean durable, boolean autoDelete, boolean internal, Map<String,Object> arguments);
```

参数介绍；

```java
exchange: 交换机名称
type: 交换机类型
		DIRECT(“direct”): 定向
		FANOUT(“fanout”): 扇形（广播），发送消息到每个与之绑定的队列
		TOPIC(“topic”): 通配符
		HEADERS(“headers”):参数匹配
durable: 是否持久化
autoDelete: 是否自动删除
internal: 是否内部使用，一般为false
arguments: 参数
```

调用exchangeDeclare函数如下：

```java
String exchangeName = “test_fanout”;
channel.exchangeDeclare(exchangeName, BuiltinExchangeType.FANOUT, true, false, false, null);
```
<br>



###### b)	创建多个队列

```java
String queue1Name = “test_fanout_queue1”;
String queue2Name = “test_fanout_queue2”;
channel.queueDeclare(queue1Name, true, false, false);
channel.queueDeclare(queue2Name, true, false, false);
```
<br>



###### c)	绑定队列和交换机
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用通道的queueBind函数进行绑定，queueBind函数的声明如下：

```java
queueBind(String queue, String exchange, String routingkey)
```

参数介绍：

```java
queue: 队列名称
exchange: 交换机名称
routingkey: 路由键，绑定规则，若交换机的类型为fanout,则routingkey设置为””，即全部发送。
```

queueBind函数的调用如下所示：

```java
channel.queueBind(queue1Name, exchangeName, “”);
channel.queueBind(queue2Name, exchangeName, “”);
```
<br>



### （二）测试生产者类
&nbsp;  &nbsp;  &nbsp;  &nbsp;执行生产者类的代码后，我们可以在Rabbitmq的管理界面看到，队列多了两个，每个队列中有同等数量的消息，因为是广播发送的。

完整的生产类代码如下：

```java
package com.rabbitmq.producer;


import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Producer_PubSub {
    public static void main(String[] args) throws TimeoutException, IOException {
        //1. 创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //2. 设置参数
        factory.setHost("192.168.211.128");
        factory.setPort(5672);
        factory.setVirtualHost("hello");
        factory.setUsername("wxx");
        factory.setPassword("wxx");
        //3.创建连接
        Connection connection = factory.newConnection();
        //4.创建通道
        Channel channel = connection.createChannel();
        //5.创建交换机
        String exchangeName = "test_fanout";
        channel.exchangeDeclare(exchangeName, BuiltinExchangeType.FANOUT, true, false, false, null);

        //6.创建队列
        String queue1Name = "test_fanout_queue1";
        String queue2Name = "test_fanout_queue2";
        channel.queueDeclare(queue1Name, true, false, false, null);
        channel.queueDeclare(queue2Name, true, false, false,   null);
        //7.绑定交换机和队列
        channel.queueBind(queue1Name, exchangeName, "");
        channel.queueBind(queue2Name, exchangeName, "");
        //6. 发送消息
        for (int i = 1; i <=10 ; i++) {
            String body = i + "Pub/Sub";
            channel.basicPublish(exchangeName, "", null, body.getBytes());
        }

        //7. 释放资源
        channel.close();
        connection.close();
    }
}
```
<br>



### （三）	Routing 路由模式
#### 1.	流程概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;路由模式中交换机与队列进行绑定时，需要指定一个RoutingKey，同时消息在发送给交换机时也必须制定消息的RoutingKey。交换机在转发消息时，根据消息的RoutingKey进行判断，只有队列的RoutingKey与消息的RoutingKey一致，才会收到消息。
<br>



#### 2.	创建交换机
&nbsp;  &nbsp;  &nbsp;  &nbsp;路由模式需要修改交换机的类型，改为DIRECT类型。

```java
String exchangeName = “test_direct”;
channel.exchangeDeclare(exchangeName, BuiltinExchangeType.DIRECT, true, false, false, null);
```
<br>



#### 3.	创建队列

```java
String queue1Name = “test_direct_queue1”;
String queue2Name = “test_direct_queue2”;
channel.queueDeclare(queue1Name, true, false, false, null);
channel.queueDeclare(queue2Name, true, false, false, null);
```
<br>



#### 4.	绑定交换机和队列
&nbsp;  &nbsp;  &nbsp;  &nbsp;这里绑定交换机和队列时我们需要制定RoutingKey，本案例的RoutingKey是日志的级别，第一个队列绑定error级别，第二个队列绑定info, error, warning级别。

```java
channel.queueBind(queue1Name, exchangeName, “error”);
channel.queueBind(queue2Name, exchangeName, “info”);
channel.queueBind(queue2Name, exchangeName , “error”);
channel.queueBind(queue2Name, exchangeName, “warning”);
```
<br>



#### 5.	发送消息
&nbsp;  &nbsp;  &nbsp;  &nbsp;路由模式下发送消息同样需要绑定RoutingKey。

```java
String body = “log:… loglevel:info”;
channel.basicPublish(exchangeName, “info”, null. body.getBytes());
```
<br>



### （四）	Topics通配符模式
&nbsp;  &nbsp;  &nbsp;  &nbsp;通配符模式即使用通配符制定规则。如news.#，凡是以news.开头的RoutingKey都能被匹配到。#代表了零到多个单词，*代表了一个单词。

<br>

