## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Apache Dubbo
### （一）	概述
#### 1.	Dubbo概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; Apache Dubbo 是一款高性能的Java RPC框架，前身是阿里巴巴公司开源的一个高性能，轻量级的开源Java RPC框架，可以和Spring框架无缝集成。
<br>



#### 2.	RPC概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; RPC全称为Remote Procedure Call，即远程过程调用，它不是一个具体的技术，而是一种网络远程调用过程概念。如两台服务器上部署着不同的应用A和B，A想要调用B应用的方法，因此需要通过网络来表达调用的语义和传达调用的数据。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 各种语言都有自己的RPC框架，Java中的RPC框架较多，广泛使用的有RMI，Hessian，Dubbo等。Dubbo提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。
<br>



#### 3.	Dubbo架构
&nbsp;  &nbsp;  &nbsp;  &nbsp; Dubbo的架构图如下所示：

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201019225124436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70#pic_center)


##### （1）	节点说明
英文 | 中文
-|-
Provider |服务的提供方
Consumer| 调用远程服务的服务消费方
Registry |服务注册与发现的注册中心
Monitor |统计服务的调用次数和调用时间的监控中心
Container |服务运行容器
<br>



##### （2）	关联分析
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1)	服务提供方在容器中启动服务，并且将服务注册到注册中心中。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2)	消费者要先调用该服务，首先要想注册中心订阅该服务，注册中心将服务提供者地址列表返回给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3)	消费者从服务提供者的地址列表中，基于软负载均衡算法，选择一台提供者进行调用，如果调用失败，再选另一台进行调用。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 4)	服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。
<br>



##### （3）	注意事项
&nbsp;  &nbsp;  &nbsp;  &nbsp; 架构图片中的虚线代表着异步访问，实现代表着同步访问，只有消费者调用服务是同步访问的，其他都是异步访问的。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 蓝色虚线代表着在启动时完成的功能，红色虚线（实现）代表着程序在运行过程中执行的功能。

<br>



## 二．	服务注册中心Zookeeper
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; Zookeeper是Apache Hadoop的子项目，是一个树形的目录服务，支持变更推送，适合作为Dubbo的注册中心。
	<br>



### （二）	Zookeeper的架构
&nbsp;  &nbsp;  &nbsp;  &nbsp; Zookeeper的属性目录架构如下图所示：

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201019225141894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70#pic_center)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，根节点为dubbo，服务层节点就是一个个的服务，每个服务下面又分为了服务提供者节点和服务消费者节点，每个提供者和消费者下又分为了多个URL地址，表示着多个对应的地址。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这样，服务提供者在服务启动时，就可以在对应的服务的提供者节点下注册自己的URL地址；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 服务消费者在启动时，向注册中心订阅需要的服务，即订阅该服务节点下的提供者节点的URL地址，同时在该服务的消费者节点下写入自己的URL地址；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 监控中心在启动时，需要订阅已启动服务节点下的所有提供者和消费者的URL地址。
<br>



### （三）	Zookeeper的安装
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	从官网下载Zookeeper的压缩包之后，将压缩包上传到linux系统中。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	解压缩压缩包

```cpp
tar -zxvf zookeeper-3.4.6.tar.gz
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	进入zookeeper-3.4.6目录，创建data目录，作为数据目录

```cpp
mkdir data
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 4.	进入conf目录，把zoo_sample.cfg改名为zoo,cfg

```cpp
cd conf
mv zoo_sample.cfg zoo.cfg
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 5.	打开zoo.cfg文件，修改data属性为刚创建的data目录路径

```cpp
dataDir=/usr/local/zookeeper-3.4.6/data
```
<br>



### （四）	Zookeeper的启动
&nbsp;  &nbsp;  &nbsp;  &nbsp; 进入Zookeeper安装目录/bin/zkServer.sh文件，后面接start表示启动服务：

```cpp
./zkServer.sh start
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 使用 status 表示查看服务状态：

```cpp
./zkServer.sh status
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 使用 stop表示停止服务

```cpp
./zkServer.sh stop
```

<br>
