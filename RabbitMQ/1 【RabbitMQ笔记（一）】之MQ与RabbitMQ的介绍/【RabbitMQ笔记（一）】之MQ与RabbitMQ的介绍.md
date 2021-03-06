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

