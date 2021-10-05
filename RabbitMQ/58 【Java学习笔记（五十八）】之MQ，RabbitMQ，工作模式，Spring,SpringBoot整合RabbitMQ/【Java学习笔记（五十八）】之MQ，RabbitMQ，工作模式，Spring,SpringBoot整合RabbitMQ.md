## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	MQ
### （一）	MQ概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;MQ全称为Message Queue(消息队列)，是在消息的传输过程中保存消息的容器，多用于分布式系统之间进行通信。分布式系统通信有两种方式，一种是直接远程调用另一种是借助中间件来完成间接通信。通信的发送方为生产者，接收方为消费者。
<br>


### （二）	消息中间件
&nbsp;  &nbsp;  &nbsp;  &nbsp;在项目中，可以将一些无需即时返回且耗时的操作提取出来，进行异步处理，而这种异步处理的方式大大地节省了服务器的请求响应时间，从而提高了系统的吞吐量。而消息中间件就是用于提供这种异步处理的。
<br>



### （三）	MQ的优势
#### 1.	任务异步处理
&nbsp;  &nbsp;  &nbsp;  &nbsp;将不需要同步处理且耗时长的操作有消息队列通知消息接收方进行异步处理，提高了应用程序的响应时间。提升了用户的体验和系统的吞吐量。
<br>



#### 2.	应用程序解耦合
&nbsp;  &nbsp;  &nbsp;  &nbsp;MQ相当于一个中介，生产方通过MQ与消费方交互，它将应用程序进行解耦合，当某一个组件出现BUG时，不会影响到另一个组件，提高了系统的容错性，同时便于系统的扩展。
<br>



#### 3.	削峰填谷
&nbsp;  &nbsp;  &nbsp;  &nbsp;在数据库遇到高并发访问时会遭遇宕机的风险，而是用了MQ之后，请求的消息被MQ保存起来，然后系统可以按照自己的消费能力来消费，比如每秒1000个数据，这样慢慢写到数据库，不会有宕机风险，这样就把并发峰值给削掉了。同时，高峰期产生的数据依然在MQ中，在之后的一段时间内，消费消息的速度依然会维持在每秒1000个，知道消费完积压的消息，这叫做填谷。提高了系统的稳定性。
<br>



### （四）	MQ的劣势
#### 1.	系统可用性降低
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于使用MQ，不仅要保障通信的系统之间的稳定，同时要保障MQ的稳定，一旦MQ宕机，就会影响业务。
<br>



#### 2.	系统复杂度提高
&nbsp;  &nbsp;  &nbsp;  &nbsp;MQ的加入提高了系统的复杂度，异步调用与同步调用是大大不同的，如何保证消息不被重复消费，消息传递的顺序性都是需要考虑的问题。
<br>



#### 3.	一致性问题
&nbsp;  &nbsp;  &nbsp;  &nbsp;异步处理的数据一致性问题十分重要，只有数据保持一致，异步处理才有意义。
<br>



### （五）	使用MQ需要满足的条件
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	生产者不需要从消费者获得反馈。因为异步处理不需要等待消费者的反馈，可以同步执行其他的操作，因此要使用MQ的条件是消费者的返回值为空。

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	MQ是通过MQ存储消息，之后让消费者处理的机制而实现的，因此一定会有处理时间的问题，使用MQ一定要容许有短暂的数据不一致性，不然就不能使用MQ。
<br>



### （六）	常见的MQ产品
&nbsp;  &nbsp;  &nbsp;  &nbsp;业界上有很多的MQ产品，如RabbitMQ, RocketMQ, ActiveMQ, Kafka, ZeroMQ, MetaMQ等等，在技术选型时，要结合自身需求及MQ产品特征选择。
#### 1.	RabbitMQ
&nbsp;  &nbsp;  &nbsp;  &nbsp;RabbitMQ是使用Erlang语言开发的，这是一款高并发的语言，因此，RabbitMQ的并发能力强，性能非常好。单级吞吐量为万级，消息延迟为微秒级。
<br>



#### 2.	ActiveMQ
&nbsp;  &nbsp;  &nbsp;  &nbsp;ActiveMQ是由Apache基金会支持的，比较成熟，文档较多，单级吞吐量为万级，消息延迟为毫秒级。
<br>



#### 3.	RocketMQ
&nbsp;  &nbsp;  &nbsp;  &nbsp;RocketMQ是阿里用Java研发的，功能比较完备，扩展性很好，单级吞吐量为十万级，消息延迟为毫秒级。
<br>



#### 4.	Kafka
&nbsp;  &nbsp;  &nbsp;  &nbsp;Kafka是由Apache基金会支持的，开发语言为Scala&Java，多用于大数据领域，单级吞吐量为十万级，消息延迟为毫秒。
<br>




## 二．	RabbitMQ
### （一）	AMQP
&nbsp;  &nbsp;  &nbsp;  &nbsp;AMQP，Advanced  Message Queuing Protocol，是一个应用层协议的开放标准，为消息中间件设计的，类比与HTTP。基于此协议的客户端和消息中间件之间可以传递消息，并不受客户端/中间件不同产品，不同开发语言的限制。

&nbsp;  &nbsp;  &nbsp;  &nbsp;AMQP的架构是生产者将消息发送到交换机中，交换机通过路由将消息存储到消息队列中，消费者再从消息队列中取出消息进行消费。
<br>



### （二）	RabbitMQ简介
&nbsp;  &nbsp;  &nbsp;  &nbsp;Rabbit技术公司在2007年基于AMQP标准开发了RabbitMQ，采用Erlang语言开发，这是一种专用于开发高并发和分布式系统的一种语言。
<br>



### （三）	RabbitMQ架构
&nbsp;  &nbsp;  &nbsp;  &nbsp;RabbitMQ的架构与AMQP的架构差不多，RabbitMQ中间件包括交换机和消息队列，只不过RabbitMQ中是由多个隔离的虚拟机组成的，每个虚拟机中有多个交换机和消息队列，每个交换机绑定不同的消息队列。

&nbsp;  &nbsp;  &nbsp;  &nbsp;为了连接的高效与节能，生产者和消费者与RabbitMQ建立连接是通过连接中的多个管道进行的，每次连接只需要单条管道。
<br>



### （四）	RabbitMQ的相关概念
#### 1.	Broker
&nbsp;  &nbsp;  &nbsp;  &nbsp;接受和分发消息的应用，就是RabbitMQ Server消息中间件。
<br>



#### 2.	Virtual host
&nbsp;  &nbsp;  &nbsp;  &nbsp;处于多用户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，当多个不同的用户使用同一个RabbitMQ Server提供的服务时，可以划分出多个Virtual host,每个用户在自己的Virtual host中创建 echange 和 queue。
<br>



#### 3.	Connection
&nbsp;  &nbsp;  &nbsp;  &nbsp;生产者，消费者和消息中间件之间的TCP连接。
<br>



#### 4.	Channel
&nbsp;  &nbsp;  &nbsp;  &nbsp;为了节省资源，提高链接的效率，在connection内部建立逻辑链接channel，如果程序支持多线程，那么每个thread可以创建单独的channel进行通讯。AMQP method包含了channel id 帮助客户端和message broker 识别 channel ，所有channel之间是完全隔离的。
<br>



#### 5.	Exchange
&nbsp;  &nbsp;  &nbsp;  &nbsp;message到达broker之后，根据分发规则，查询表中的routing key, 分发消息到queue中，常见的有direct, topic, fanout。
<br>



#### 6.	Queue
&nbsp;  &nbsp;  &nbsp;  &nbsp;消息从Exchange 被发送到Queue中存储，等待被消费者取走。
<br>



#### 7.	Binding
&nbsp;  &nbsp;  &nbsp;  &nbsp;交换机和队列之间的虚拟链接，binding中可以包含routing key。binding信息被保存到exchange的查询表中，用于消息的分发依据。
<br>



### （五）	RabbitMQ的工作模式
&nbsp;  &nbsp;  &nbsp;  &nbsp;简单模式，work queues，Publish/Subscribe发布与订阅模式，Routing路由模式，Topic主题模式，RPC远程调用模式。
<br>



### （六）	JMS
&nbsp;  &nbsp;  &nbsp;  &nbsp;JMS是Java消息服务（JavaMessage Service）应用程序接口，是一个Java平台中关于面向消息中间件的API，类比与JDBC，很多消息中间件都实现了JMS规范，如ActiveMQ，但是RabbitMQ官方并没有提供JMS的实现包，只有开源社区中有。
<br>



### （七）	安装rabbitmq与erlang
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于安装rabbitmq需要首先安装erlang，因此我们首先在ubuntu上安装erlang。注意，安装环境为ubuntu16.04,。
<br>



#### 1.	安装erlang

sudo apt-get install erlang-nox

```cpp
在这里插入代码片
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果在安装过程中报错：ubuntu16.04 E: dpkg 被中断，请输入`sudo dpkg –configure -a` 解决此问题。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果这样依旧不行，删除掉然后重新即可。

```cpp
sudo rm /var/lib/dpkg/updates/*
sudo apt-get update
```
<br>



#### 2.	安装rabbitmq

```cpp
sudo apt-get upgrade
sudo qpt-get install rabbitmq-server
```
<br>



#### 3.	rabbitmq管理

```cpp
# 启动rabbitmq服务
sudo service rabbitmq-server start 
# 关闭rabbitmq服务
sudo service rabbitmq-server stop
# 重启服务
sudo service rabbitmq-server restart
# 查看服务运行状态
sudo service rabbitmqctl status
```
<br>



#### 4.	安装web管理插件

```cpp
sudo rabbitmqctl start_app
sudo rabbitmq-plugins enable rabbitmq_management
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;安装完插件后，可以通过在ubuntu中通过网址http://127.0.0.1:15672进行访问，初始的用户名和密码都是guest，如果登录失败，说明没有开启超级管理员权限。需要新建用户并授予超级管理员权限：

&nbsp;  &nbsp;  &nbsp;  &nbsp;新建用户

```cpp
sudo rabbitmqctl add_user  admin  admin  
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;赋予权限

```cpp
sudo rabbitmqctl set_user_tags admin administrator 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;赋予virtual host中所有资源的配置、写、读权限以便管理其中的资源，也是添加远程访问权限

```cpp
sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;若遇到Config file配置文件找不到的情况，可以到rabbitmq的默认安装路径/usr/share/doc/rabbitmq-server中将rabbitmq.config复制到/etc/rabbitmq/rabbitmq.config中
cd /usr/share/doc/rabbitmq-server
cp rabbitmq.config /etc/rabbitmq/rabbitmq.config

&nbsp;  &nbsp;  &nbsp;  &nbsp;这样重启rabbitmq-server服务后，配置文件配置成功了

```cpp
service rabbitmq-server restart
```
<br>



### （八）	rabbitmq命令行操作

```cpp
sudo rabbitmqctl # 查看所有命令和帮助文档
sudo rabbitmqctl stop # 停止服务
sudo rabbitmqctl status # 查看服务状态
sudo rabbitmqctl list_users # 查看当前所有用户
sudo rabbitmqctl list_user_permissions guest # 查看默认guest用户的权限
sudo rabbitmqctl delete_user guest # 删掉默认用户(由于RabbitMQ默认的账号用户名和密码都是guest。为了安全起见, 可以删掉默认用户）
sudo rabbitmqctl add_user username password # 添加新用户
sudo rabbitmqctl set_user_tags username administrator# 设置用户tag
sudo rabbitmqctl set_permissions -p / username ".*" ".*" ".*" # 赋予用户默认vhost的全部操作权限
sudo rabbitmqctl list_user_permissions username # 查看用户的权限
rabbitmqctl set_user_tags User Tag
User为用户名， Tag为角色名(对应于上面的administrator，monitoring，policymaker，management，或其他自定义名称)。
```
<br>



### （九）	rabbitmq的权限角色
#### 1.	超级管理员（administrator）
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以登录管理控制台查看所有的信息，对用户，策略进行操作。

<br>


#### 2.	监控着（monitoring）
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以登录管理控制台查看rabbitmq节点的相关信息（进程数，内存使用情况，磁盘使用情况）
<br>



#### 3.	策略制定者（policymaker）
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以登录管理控制台，对策略进行管理，但无法查看节点的相关信息。
<br>



#### 4.	普通管理者（management）
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以登录管理控制台，无法看淡节点信息，也无法对策略进行管理。
<br>



#### 5.	其他
&nbsp;  &nbsp;  &nbsp;  &nbsp;无法登录管理控制台，一般就是生产者和消费者。
<br>




## 三．	Rabbitmq项目实践
### （一）	创建模块，配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp;Rabbitmq连接着生产者和消费者，因此创建两个maven模块，一个是生产者producer，一个是消费者consumer。在配置文件pom.xml中添加rabbitmq的客户端依赖。

```xml
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.6.0</version>
</dependency>
```
<br>



### （二）	创建生产者类
#### 1.	流程概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;在Rabbitmq的流程中，生产者通过连接将消息传输到 Rabbitmq服务器中，其中包括创建连接工厂，设置通信的参数如ip，端口等，之后创建连接，创建连接中的通道，在复杂的情况下还需要创建 交换机，最后创建队列，一切准备就绪后就可以发送消息了。
<br>



#### 2.	创建连接工厂

```java
ConnectionFactory factory = new ConnectionFactory();
```
<br>



#### 3.	设置连接的参数

```java
factory.setHost(“192.168.211.128”); //设置host
factory.setPort(5672); //设置端口，默认5672
factory.setVirtualHost(“/hello”); //设置虚拟机，默认值为/
factory.setUsername(“wxx”); //设置用户名，默认guest
factory.setPassword(“wxx”); // 设置密码，默认guest
```
<br>



#### 4.	创建连接Connection

```java
Connection connection = factory.newConnection();
```
<br>



#### 5.	创建Channel

```java
Channel channel = connection.createChannel();
```
<br>



#### 6.	创建队列Queue

```java
channel.queueDeclare(“hello_world”, true, false, false, null);
```

queueDeclare函数的声明如下：

```java
queueDeclare(String queue, Boolean durable, Boolean exclusive, Boolean autoDelte, Map<String,Object> argument);
```

该函数参数介绍如下：

```xml
queue: 队列名称
durable:是否持久化，即是否保存到硬盘中
exclusive:
		是否独占，只能有一个消费者监听这个队列
		当Connection关闭时，是否删除队列
autodelete: 当没有Consumer时，是否自动删除队列。
arguments: 参数
```
<br>



#### 7.	发送消息
&nbsp;  &nbsp;  &nbsp;  &nbsp;生产者发送消息使用的是通道的basicPublish函数，该函数的声明如下：

```java
basicPublish(String exchange, String routingKey, BasicProperties pros, byte[] body)
```

参数的介绍如下：

```java
exchange : 交换机名称，简单模式下交换机会使用默认的””，即不使用交换机。
routingKey: 路由名称，在简单模式下不使用交换机，则路由名称直接用队列的名称。
props: 配置信息
body: 发送的消息数据，注意是byte[]格式的。
```

```java
String body = “hello world”;
channel.basicPublish(“”, “hello_world”, null, body.getBytes());
```
<br>



#### 8.	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;执行生产者类代码后，我们发现在Rabbitmq的管理界面中多了一条队列，说明执行成功。
<br>



### （三）	创建消费者类
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;消费者类中的内容是消费者监听Rabbitmq服务器，从队列中通过连接的通道监听消息，并且消费消息的过程。因此，创建连接，创建通道的操作与生产者都是一样的，后面的从通道接受消息操作是不同的。
<br>



#### 2.	接受消息
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用通道的basicConsume函数，该函数的声明如下：

```java
basicConsume(String queue, Boolean autoAck, Consumer callback)
```

参数介绍：

```java
queue: 队列名称
autoAck: 是否自动确认
callback: 回调对象
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这个回调对象是一个Consumer接口，这个接口有个实现类DefaultConsumer，该实现类有唯一一个构造方法，通过传入通道进行构造。我们需要创建一个DefaultConsumer实现类，并且重写它的handleDelivery方法，这是一个回调方法，当收到信息后，会自动执行该方法。

handDelivery函数的声明如下：

```java
handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body);
```

参数介绍：

```java
consumerTag: 消息的标识
envelope: 获取一些信息，我们可以从中取出信息，如交换机，路由key
properties: 配置信息
body: 数据
```

```java
Consumer consumer = new DefaultConsumer(channel){
		@Override
		public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body){
		System.out.println(“consumerTag:” + consumerTag);
		System.out.println(“Exchange”+ envelope.getExchange());
		System.out.println(“RoutingKey” + envelope.getRoutingKey());
		System.out.println(“properties” +properties);
		System.out.println(“body” +new String(body));
		}

};
channel.basicConsume(“hello_world”, true, consumer);
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，消费者是一个监听程序，不需要关闭资源！！！
<br>



#### 3.	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;执行消费者代码后，在Rabbitmq管理界面中看到之前生成的队列已经被取走了，说明消费者执行成功。

<br>



## 四．	Rabbitmq的工作模式
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



## 五．	Spring整合RabbitMQ
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;所谓的用Spring来整合Rabbitmq，就是Rabbitmq所需要的一些配置先在配置文件中设置好，在代码中需要使用时直接注入到Spring容器中，这样就可以大大地减少代码量。
<br>



### （二）	Spring整合Rabbitmq生产者类
#### 1.	导入依赖坐标
&nbsp;  &nbsp;  &nbsp;  &nbsp;导入Spring的坐标，以及rabbitmq整合的相关坐标。

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.1.17.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.amqp</groupId>
        <artifactId>spring-rabbit</artifactId>
        <version>2.1.8.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.1.7.RELEASE</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```
<br>



#### 2.	rabbitmq的配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;将连接rabbitmq所需要参数全部都配置到一个properties文件中，如ip地址，端口号，用户名，密码，虚拟机名称。

```xml
rabbitmq.host=192.168.211.100
rabbitmq.port=5672
rabbitmq.username=
rabbitmq.password=
rabbitmq.virtual-host=/
```
<br>



#### 3.	Spring的核心配置文件
##### （1）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个配置文件就将我们在使用rabbitmq时所用到的连接，交换机，队列等等都配置到了Spring容器中，这样，当我们需要使用这些组件时直接注入到容器中即可使用，非常方便。
<br>



##### （2）	加载配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们之前抽去了rabbitmq的配置文件，而在Spring的配置文件中我们需要使用到rabbitmq的一些参数，因此我们需要加载rabbitmq.properties配置文件。

```xml
<context:property-placeholder location=”classpath:rabbitmq.properties”/>
```
<br>



##### （3）	定义rabbitmq connectionFactory
&nbsp;  &nbsp;  &nbsp;  &nbsp;连接由连接工厂产生，因此我们需要定义一个连接工厂，该连接工厂的参数都是由rabbitmq.properties配置文件的参数提供的，用于连接到rabbitmq服务器。

```xml
<rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                           port="${rabbitmq.port}"
                           username="${rabbitmq.username}"
                           password="${rabbitmq.password}"
                           virtual-host="${rabbitmq.virtual-host}"/>
```
<br>



##### （4）	定义管理交换机，队列的admin工厂
&nbsp;  &nbsp;  &nbsp;  &nbsp;交换机，队列都需要进行管理，因此设置一个管理的工厂就是我们上面定义的连接工厂。

```xml
<rabbit:admin connection-factory="connectionFactory"/>
```
<br>



##### （5）	定义持久化队列，不存在则自动创建
&nbsp;  &nbsp;  &nbsp;  &nbsp;这些队列会自动创建，如果没有被绑定到响应的交换机中，它们会默认绑定到默认的交换机，默认的交换机类型为direct，名称为“”，路由键为队列的名称。

```xml
<rabbit:queue id="spring_queue" name="spring_queue" auto-declare="true"/>
<rabbit:queue id="spring_fanout_queue_1" name="spring_fanout_queue_1" auto-declare="true"/>
<rabbit:queue id="spring_fanout_queue_2" name="spring_fanout_queue_2" auto-declare="true"/>
```
<br>



##### （6）	定义广播类型交换机，绑定队列
&nbsp;  &nbsp;  &nbsp;  &nbsp;广播类型的交换机，绑定不需要路由键。

```xml
<rabbit:fanout-exchange id="spring_fanout_exchange" name="spring_fanout_exchange" auto-declare="true">
    <rabbit:bindings>
        <rabbit:binding queue="spring_fanout_queue_1"/>
        <rabbit:binding queue="spring_fanout_queue_2"/>
    </rabbit:bindings>
</rabbit:fanout-exchange>
```
<br>



##### （7）	定义用于Topic交换机的持久化队列与Topic交换机
&nbsp;  &nbsp;  &nbsp;  &nbsp;持久化队列的创建都是一样的，关键是不同交换机的创建是不同的，topic类型的交换机在绑定队列时需要设置路由键的规则。

```xml
<rabbit:queue id="spring_topic_queue_star" name="spring_topic_queue_star" auto-declare="true"/>
<rabbit:queue id="spring_topic_queue_well" name="spring_topic_queue_well" auto-declare="true"/>
<rabbit:queue id="spring_topic_queue_well2" name="spring_topic_queue_well2" auto-declare="true"/>

<rabbit:topic-exchange id="spring_topic_exchange" name="spring_topic_exchange" auto-declare="true">
    <rabbit:bindings>
        <rabbit:binding pattern="xx.*" queue="spring_topic_queue_star"/>
        <rabbit:binding pattern="xx.#" queue="spring_topic_queue_well"/>
        <rabbit:binding pattern="wxx.#" queue="spring_topic_queue_well2"/>
    </rabbit:bindings>
</rabbit:topic-exchange>
```
<br>



##### （8）	定义rabbitTemplate对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;最重要的一个对象是定义rabbitTemplate对象，该对象用于在代码中发送消息，只要我们将该对象注入到容器中就可以使用了。

```xml
<rabbit:template id=”rabbitTemplate” connection-factory=”connectionFactory”/>
```
<br>



#### 4.	测试生产者类
&nbsp;  &nbsp;  &nbsp;  &nbsp;在test包内进行测试。

##### （1）	导入注解@RunWith
&nbsp;  &nbsp;  &nbsp;  &nbsp;测试需要使用注解 

```java
@RunWith(SpringJUnit4ClassRunner.class)
```
<br>



##### （2）	导入注解@ContextConfiguration
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们需要加载Spring的配置文件。因此，使用注解`@ContextConfiguration(location=”classpath:spring-rabbitmq-producer.xml”)`，括号内为配置文件的地址。
<br>



##### （3）	注入RabbitmqTemplate对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用该对象来发送消息。

```java
@Autowired
private RabbitTemplate rabbitTemplate;
```
<br>



##### （4）	发送消息
&nbsp;  &nbsp;  &nbsp;  &nbsp;发送消息使用rabbitTemplate对象的convertAndSend方法，该方法有很多重载，任君选择。

简单模式：

```java
rabbitTemplate.convertAndSend(“spring_queue”, “hello_world”);
```

广播模式：

```java
rabbitTemplate.convertAndSend(“spring_fanout_exchange”, “”, “hello_fanout”);
```

通配符模式：

```java
rabbitTemplate.convertAndSend(“spring_topic_exchange”, “xx.xx”, “hello_topic”);
```
<br>



### （三）	Spring整合rabbitmq消费者类
#### 1.	配置pom.xml文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;配置方法与生产者类相同。
<br>



#### 2.	配置Spring核心配置文件
##### （1）	定义rabbitmq的连接工厂
&nbsp;  &nbsp;  &nbsp;  &nbsp;定义方法与生产者相同。
<br>



##### （2）	定义监听类的Bean对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;Bean对象的id是自己定义的，class是监听类的全包名。

```xml
<bean id="springQueueListener" class="com.rabbitmq.listener.SpringQueueListener"/>
```
<br>



##### （3）	定义监听器的容器
&nbsp;  &nbsp;  &nbsp;  &nbsp;容器中存储着监听器，每个监听器监听着对应的队列。

```xml
<rabbit:listener-container connection-factory="connectionFactory" auto-declare="true">
    <rabbit:listener ref="springQueueListener" queue-names="spring_queue"/>
    <rabbit:listener ref="fanoutListener1" queue-names="spring_fanout_queue_1"/>
</rabbit:listener-container>
```
<br>



#### 3.	创建监听类
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们在Spring的配置文件中定义了监听类的Bean对象，监听类会一直持续地监听队列的消息，若有消息，就会调用回调函数，作出相应的操作。

&nbsp;  &nbsp;  &nbsp;  &nbsp;监听类需要实现MessageListener接口，该接口有 重载方法onMessage，这个方法就是一个回调函数，负责响应监听者获取了队列中消息，作出相应的操作。

```java
package com.rabbitmq.listener;

import org.springframework.amqp.core.Message;
import org.springframework.amqp.core.MessageListener;

public class SpringQueueListener implements MessageListener {
    @Override
    public void onMessage(Message message) {
        System.out.println(new String(message.getBody()));
    }
}
```
<br>



#### 4.	测试消费者类
&nbsp;  &nbsp;  &nbsp;  &nbsp;在测试类中进行测试。

### （四）	Spring配置文件详解
#### 1.	rabbit约束头
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring配置文件中导入了rabbit约束头以及rabbit约束文件，因此可以使用rabbit命名空间，如下所示：

```java
<rabbit:connection-factory>
```
<br>



#### 2.	定义队列的属性
&nbsp;  &nbsp;  &nbsp;  &nbsp;定义队列时，我们会设置一些属性，与之前没有使用Spring时的代码参数差不多，如下所示：

```java
id: bean的名称
name: queue的名称
auto-declare: 自动创建
auto-delete: 自动删除，最后一个消费者和该队列断开连接后，自动删除队列。
exclusive: 是否独占
durable: 是否持久化
```

<br>



## 六．	SpringBoot整合RabbitMQ生产者
### （一）	创建SpringBoot工程
&nbsp;  &nbsp;  &nbsp;  &nbsp;添加一个maven模块。
<br>



### （二）	配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp;SpringBoot项目需要在pom.xml中继承父工程依赖，以及rabbitmq的依赖包。

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.4.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
</dependencies>
```
<br>



### （三）	配置application.yml
&nbsp;  &nbsp;  &nbsp;  &nbsp;application.yml中配置了连接rabbitmq的连接参数，直接输入rabbitmq就会有提示，不用担心找不到参数名称的问题。

```xml
spring:
  rabbitmq:
    host: 192.168.211.100
    port: 5672
    username: 
    password: 
    virtual-host: /
```
<br>



### （四）	创建启动类
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建SpringBoot项目的入口，即 启动类。

```java
package com.rabbitmq;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ProducerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProducerApplication.class);
    }
}
```
<br>



### （五）	创建配置类
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring将交换机，队列等配置在xml文件中，SpringBoot将它们配置在一个配置类中，使用注解的方式。
<br>



#### 1.	首先在该配置类上添加@Configuration表示这是一个配置类
<br>


#### 2.	配置交换机
&nbsp;  &nbsp;  &nbsp;  &nbsp;添加一个方法，返回交换机对象，并且在该方法上添加@Bean(“bootExchange”)注解，表示这是一个id为bootExchange的Bean对象。
	
&nbsp;  &nbsp;  &nbsp;  &nbsp;那么，交换机对象如何生成呢？通过ExchangeBuilder可以创建多种类型的交换机，同时可以设置它的属性，最终通过build()函数生成交换机。

```java
public static final String EXCHANGE_NAME = "boot_topic_exchange";
public Exchange bootExchange(){
    return ExchangeBuilder.topicExchange(EXCHANGE_NAME).durable(true).build();
}
```

<br>



#### 3.	配置队列
&nbsp;  &nbsp;  &nbsp;  &nbsp;同理，使用同样的方法配置队列。

```java
public static final String QUEUE_NAME = "boot_queue";
@Bean("bootQueue")
public Queue bootQueue(){
    return QueueBuilder.durable(QUEUE_NAME).build();
}
```
<br>



#### 4.	绑定交换机和队列
&nbsp;  &nbsp;  &nbsp;  &nbsp;同样的，添加注解@Bean，不过这次不需要id，因为这个绑定关系是不需要注入进去的。要绑定交换机和队列，就需要知道具体的交换机和队列的名称，因此，我们需要在该函数的参数中传入交换机和队列，并且用@Qualifier注解通过id进行区分不同的交换机和队列。

&nbsp;  &nbsp;  &nbsp;  &nbsp;函数体的内容是使用BindingBuiler的bind方法绑定队列和交换机，以及后面的属性设置。

```java
@Bean
public Binding bingQueueExchange(@Qualifier("bootQueue")Queue queue, @Qualifier("bootExchange")Exchange exchange){
    return BindingBuilder.bind(queue).to(exchange).with("boot.#").noargs();
}
```
<br>



### （六）测试生产者类
&nbsp;  &nbsp;  &nbsp;  &nbsp;定义完所有的配置后，我们可以编写代码进行测试生产者发送消息的情况了，在test包下创建一个测试类，通过注解的方式进行注入rabbitTemplate，使用该对象发送消息。

```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class ProducerTest {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void test(){
        rabbitTemplate.convertAndSend(EXCHANGE_NAME, "boot.haha", "hello_world");
    }

}
```

<br>



## 七．	SpringBoot整合RabbitMQ消费者
&nbsp;  &nbsp;  &nbsp;  &nbsp;消费者中同样要配置pom.xml，application.yml文件，但是不需要设置配置类。同时消费者还要设置监听类，对相应的队列进行监听工作。

&nbsp;  &nbsp;  &nbsp;  &nbsp;使用注解@Component表示这是一个Bean对象，创建一个监听方法，在其上添加注解@RabbitListener，在该注解的参数中需要传入监听的队列的名称（不是id）。在该监方法的参数中传入一个Message对象，该消息对象用于存储从队列中监听到的消息，用于方法体中的操作。

```java
@Component
public class RabbitMQListener{
	@RabbitListener(queue=”boot_queue”)
	public void ListenerQueue(Message message){
		System.out.println(message);
	}

}
```

注意，这个监听类要和入口类在同一个包下才能被扫描到。

















