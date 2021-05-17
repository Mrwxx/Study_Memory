## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	日志与监控
### （一）	Rabbitmq日志
&nbsp;  &nbsp;  &nbsp;  &nbsp; 日志记录了客户端和Rabbitmq服务器的交互情况，包含RabbitMQ的版本号，Erlang的版本号，RabbitMQ的服务节点名称，cookie的hash值，RabbitMQ配置文件地址，内存限制，硬盘限制。日志默认存放在 /var/log/rabbitmq/rabbit@xxx.log。
<br>



### （二）	rabbitmqctl管理监控
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们可以通过rabbitmq的管理控制台直接查看，也可以通过命令的方式来监控。
<br>



#### 1.	查看队列

```xml
rabbitmqctl list_queues
```
<br>



#### 2.	查看交换机

```xml
rabbitmqctl list_exchanges
```
<br>



#### 3.	查看用户

```xml
rabbitmqctl list_users
```
<br>



#### 4.	查看连接

```xml
rabbitmqctl list_connections
```
<br>



#### 5.	查看消费者信息

```xml
rabbitmqctl list_consumers
```
<br>



#### 6.	查看环境变量

```xml
rabbitmqctl environment
```
<br>



#### 7.	查看未被确认的队列

```xml
rabbitmqctl list_queues name messages_unacknowledged
```
<br>



#### 8.	查看单个队列的内存使用情况

```xml
rabbitmqctl list_queues name memory
```
<br>



#### 9.	查看准备就绪的队列

```xml
rabbitmqctl list_queus name messages_ready
```
<br>



## 二．	消息追踪
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 消息传递的过程中总会有各种各样的异常发生，我们需要通过追踪这些消息的发送过程，投递过程，来定位消息传递的异常位置，提高开发的效率。
<br>



### （二）	Firehose和rabbitmq_tracing插件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在Rabbiitmq中可以使用Firehose和rabbitmq_tracing插件来实现消息的追踪功能。
#### 1.	Firehose
##### （1）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; Firehose的机制是将生产者发送给Rabbitmq的消息以及Rabbitmq投递给消费者的消息按照指定的格式发送到默认的交换机中。这个默认的交换机的名称为amq.rabbitmq.trace，它是一个topic类型的交换机。同时，这个消息会在原有的消息基础上添加消息头，包括一系列的消息相关的信息，如交换机名称，路由键，节点名称，虚拟机名称，连接名称，通道数目，用户名称，路由的队列名称。这些信息能够有效地追踪消息的发送过程。
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 发送到amq.rabbitmq.trace默认交换机的消息的routing key 为两种，一种是生产者投递到交换机的消息，routing key 为publish.exchangename，后面为实际的交换机名称；一种是消费者从queue中获取的消息，routing key为deliver.queuname，后面为实际的队列名称。

<br>



##### （2）开启插件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过rabbitmqctl trace_on 开启Firehose命令

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过rabbitmqctl trace_off 关闭Firehose命令

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，打开该插件会影响消息的写入功能，请合理地使用。
<br>



#### 2.	rabbitmq_tracing
##### （1）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; rabibtmq_tracing比Firehose多了一层GUI的封装，更容易使用了。
<br>



##### （2）启用插件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 启用插件： `rabbitmq-plugins enable rabbitmq_tracing`
<br>



##### （3）使用插件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当我们启用该插件后，在rabbitmq的管理控制台的admin子页面就会出现trace的入口，我们自行添加一个trace，选择要监听的虚拟机，以及routing key规则。设置好规则后，那些被Firehose默认发送到amq.rabbit,mq.trace交换机中的消息只要符合trace的routing key规则都会被记录下消息的消息头到日志文件trace.log中。这样就记录下了消息的发送与投递过程，便于我们的开发纠错。

<br>



## 三．	消息可靠性保障（消息补偿）
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如何保障消息能够全部发送出去且全部被消费成功呢？

&nbsp;  &nbsp;  &nbsp;  &nbsp; 首先，消息从生产者端被保存到业务数据库中，同时发送到Rabbitmq的服务器中，消费者从队列中取出消息，消费成功后，发送确认消息给队列，这时，一个回调检查服务监听到了消息的确认，将该消息存储到回调检查的数据库中。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 生产端在发送完该消息后，会延迟一段时间再次发送该消息，回调检查服务通过监听该延迟消息，对比该延迟消息是否存在于回调检查的数据库中，如果存在，说明该消息已经消费成功，对于延迟消息不做处理；如果不存在，则调用生产者重新发送该消息。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果，延迟发送的消息也失败了，那么还有最后一个保障：定时检查服务，我们定时地对比生产者端发送消息的业务数据库以及回调检查服务的数据库，如果数据由确实，那么就会调用生产者，重新发送消息。

<br>



## 四．	消息幂等性保障
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 幂等性指的是一次或者多次请求某一个资源，对于该资源来说应该得到同样的结果。在MQ中指的是消费多条相同的消息，得到与消费该消息一次相同的结果。
<br>



### （二）	乐观锁机制
&nbsp;  &nbsp;  &nbsp;  &nbsp; 假设有一条消息发送到RabbitMQ中，该消息是为了将数据库中的金额减去500元，然而，消费者消费出错，导致该消息被重新发送了多次，那么在没有消息幂等性保障的情况下被消费了多次，导致业务逻辑出错。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 乐观锁机制是解决消息幂等性问题的，它给消息加上了version版本号，根据version来设定消费的规则，如设定version=1时代表着这是第一次消费该消息。当同样的消息被消费过一次后，将version加1，那么下次同样的消息过来后，就无法匹配上消费的规则，自然就无法被消费。















