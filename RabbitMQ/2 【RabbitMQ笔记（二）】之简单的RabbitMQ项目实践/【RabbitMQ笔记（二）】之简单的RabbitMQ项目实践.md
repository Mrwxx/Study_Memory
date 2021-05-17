## 一．	Rabbitmq项目实践
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

