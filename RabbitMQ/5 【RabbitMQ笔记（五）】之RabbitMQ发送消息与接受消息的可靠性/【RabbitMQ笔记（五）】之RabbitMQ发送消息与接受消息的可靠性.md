## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	Rabbitmq发送消息的可靠性
### （一）	消息的可靠投递
&nbsp;  &nbsp;  &nbsp;  &nbsp; Rabbitmq为我们提供了两种方式来控制消息的投递可靠性，分别是confirm确认模式，return退回模式。我们将利用这两个callback来控制消息点的可靠性投递。
<br>


#### 1.	confirm模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当消息从生产者发送到交换机时，不论发送是否成功都会返回一个confirmCallback，当发送成功时这个confirmCallback为true，当发送失败时这个confirmCallback为false。
<br>



#### 2.	return模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; 消息从交换机发送到队列时，若投递失败则会返回一个returnCallback。
<br>



### （二）	confirm模式实例
#### 1.	创建Maven项目
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建普通的maven项目。
<br>



#### 2.	配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 添加Spring依赖，Rabbitmq依赖。

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



#### 3.	配置rabbitmq连接配置
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将rabbitmq的连接信息都抽取到一个properties文件中。

```xml
rabbitmq.host=192.168.211.100
rabbitmq.port=5672
rabbitmq.username=
rabbitmq.password=
rabbitmq.virtual-host=/
```
<br>



#### 4.	配置Spring核心配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将交换机，队列，连接工厂，rabbitTemplate首先配置到容器中，方便之后直接注入容器。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:rabbitmq.properties"/>

    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>

    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>

<!--    消息可靠性投递（生产端）-->
    <rabbit:queue id="test_queue_confirm" name="test_queue_confirm"></rabbit:queue>
    <rabbit:direct-exchange name="test_exchange_confirm">
        <rabbit:bindings>
            <rabbit:binding queue="test_queue_confirm" key="confirm"></rabbit:binding>
        </rabbit:bindings>
    </rabbit:direct-exchange>

</beans>
```
<br>



#### 5.	测试Confirm模式
##### （1）开启confirm模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如何测试Confirm模式呢？首先我们要开启Confrim模式，在Spring的配置文件中，将connectionFactory的配置添加publisher-confirms=”true”，即开启confirm模式。
<br>



##### （2）在rabbitTemplate中定义ConfrimCallBack回调函数
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们在测试类中注入rabbitTemplate之后，需要使用它的setConfirmCallback方法来定义回调函数。该回调函数有一个函数confirm需要重写，函数体就是当消息从发送方发送后执行操作，该confirm函数有三个参数：

* @param correlationData   相关配置信息
* @param ack   交换机是否成功收到了消息
* @param cause 失败的原因

&nbsp;  &nbsp;  &nbsp;  &nbsp; 根据ack的值，来判断交换机是否成功收到了消息，作出相应的操作。
<br>



### （三）	Return模式实例
#### 1.	开启回退模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同样是在connection-factory的配置中添加publisher-return=”true”。
<br>



#### 2.	设置回调函数
&nbsp;  &nbsp;  &nbsp;  &nbsp; 使用rabbitTemplate的setReturnCallback函数来设置回调函数，当消息成功地从交换机发送到队列时，该回调函数不会执行；当消息没有发送到队列时，该消息默认会被丢弃，则回调函数也不会执行。要想执行该回调函数，就得设置交换机处理失败消息的模式。我们可以在回调函数中设置当生产者监听到返回的消息时该做出什么样的操作。

```java
rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
    @Override
    public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
        System.out.println("return 执行了");
        System.out.println(message);
        System.out.println(replyCode);
        System.out.println(replyText);
        System.out.println(exchange);
        System.out.println(routingKey);
    }
});
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 该returnMessage函数的参数如下所示：

```xml
message: 消息对象
replyCode: 错误码
replyText: 错误信息
exchange: 交换机
reoutingKey: 路由键
```
<br>



#### 3.	设置交换机处理失败消息的模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置完后，交换机就会将发送失败的消息返回给生产者，生产者通过回调函数就会得到该消息，作出相应的操作。

```java
rabbitTemplate.setMandatory(true);
```
<br>



#### 4.	事务模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; RabbitmQ中也有事务模式，使用通道的如下方式，完成事务控制：

```xml
txSelect()：将当前通道设置为事务模式
txCommit(): 发送成功提交事务
txRollback(): 发送失败回滚事务
```

<br>



## 二．	RabbitMQ的消费端的可靠性
### （一）	Consumer Ack
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当消息从RabbitMQ发送到消费端后，消费端收到消息后通过Ack的方式确认。共有三种确认方式：
<br>



#### 1.	自动确认
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当消费端接受到消息后，就自动发送回执给RabbitMQ说明该消息已经收到了，不论该消息后面的处理是否成功，若后面的消息处理步骤是失败的，那么该消息等于被丢弃了。

```xml
acknowledge=”none”
```
<br>



#### 2.	手动确认
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当消费端接受到消息后，不会自动地发送回执，而是等到后面的消息处理阶段成功后，再手动通过调用通道的basicAck()方法发送回执给RabbitMQ；若后面的消息处理失败了，我们也可以通过调用通道的basicNack()方法让其自动重新发送消息。

```xml
acknowledge=”manaul”
```
<br>



#### 3.	根据异常情况确认

```xml
acknowledge=”auto”
```
<br>



### （二）	Ack自动确认实例
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在消费端进行Ack实例测试。

#### 1.	创建Maven项目
<br>



#### 2.	配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同生产者。
<br>



#### 3.	配置rabbitmq.properties
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同生产者。
<br>



#### 4.	配置Spring核心配置
##### （1）	加载rabbitmq.properties文件

```xml
<context:property-placeholder location="classpath:rabbitmq.properties"/>
```
<br>



##### （2）	定义connectionFactory

```xml
<rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                           port="${rabbitmq.port}"
                           username="${rabbitmq.username}"
                           password="${rabbitmq.password}"
                           virtual-host="${rabbitmq.virtual-host}"
                           publisher-confirms="true"
                           publisher-returns="true"

/>
```
<br>



##### （3）	设置包扫描
&nbsp;  &nbsp;  &nbsp;  &nbsp; 用包扫描的方式，就不需要在Spring配置文件中定义监听器了，直接在监听器类上添加@Component注解即可。

```xml
<context:component-scan base-package="com.rabbitmq.listener"/>
```
<br>



##### （4）	定义监听器容器
&nbsp;  &nbsp;  &nbsp;  &nbsp; 监听器容器存储着所有的监听器。

```xml
<rabbit:listener-container connection-factory="connectionFactory">
    <rabbit:listener ref="" queues="" ></rabbit:listener>
</rabbit:listener-container>
```
<br>



##### 5.	创建自动确认监听类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建一个自动确认监听类用于消费者监听队列，该监听类需要实现MessageListener接口，重写了onMessage方法，该方法用于监听到消息进行响应操作。注意，该方法上需要添加@Component注解，以便能够被扫描到。

```java
@Component
public class AckListener implements MessageListener {
    @Override
    public void onMessage(Message message) {
        System.out.println(new String(message.getBody()));
    }
}
```
<br>



#### 6.	测试类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在test包下创建一个测试类进行不间断地监听测试。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-producer.xml")
public class ConsumerTest {
    @Test
    public void test(){
        while(true){

        }
    }
}
```
<br>



### （三）	Ack手动确认实例
&nbsp;  &nbsp;  &nbsp;  &nbsp; 其他的设置同自动确认都是相同的，不同的只有监听类与监听类容器的配置不同。

#### 1.	监听容器
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在Spring核心配置文件中配置监听类的容器时，需要添加一个acknowledge=”manual”属性，默认是none。

```xml
<rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual">
    <rabbit:listener ref="ackListener" queue-names="test_queue_confirm" ></rabbit:listener>
</rabbit:listener-container>
```
<br>



#### 2.	监听类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 手动确认的监听类在消息处理成功后通过调用通道的basicAck()方法来返回确认给RabbitMq，因此，在监听类中需要使用通道这个参数，然而我们实现的MessageListener接口的onMesage方法是没有通道这个参数的。因此，我们需要寻找MessageListener接口的子接口ChannelAwareMessageListener，它的onMessage方法有通道参数，因此将实现MessageListener接口改为实现channelAwareMessageListener接口。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 该onMessage方法体中有3段代码，一段是用于接收消息，一段是用于处理业务逻辑，一段是根据消息处理成功与否来执行返回确认操作或者重新发送消息。通道的basicAck()方法中有两个参数：deliveryTag，multiple。deliveryTag表示当前收到的消息的标签，multiple若为true表示允许多条消息同时被签收。要使用deliveryTag，首先要从消息中取出该消息的标签，通过getMessageProperties().getDeliveryTag()获取标签。通道的另一个方法basicNack()还有另外一个参数requeue，若为true表示消息处理失败后能够重新回到queue中，rabbitMQ会重新发送该消息给消费者。

```java
@Component
public class AckListener implements ChannelAwareMessageListener {
    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        //获取消息的Tag
        long deliveryTag = message.getMessageProperties().getDeliveryTag();

        try {
            //接受消息
            System.out.println(new String(message.getBody()));

            //处理业务逻辑
            System.out.println("业务逻辑");
            //int i = 3 / 0;
            //手动签收
            channel.basicAck(deliveryTag, true);
        } catch (Exception e) {
            //e.printStackTrace();

            //拒绝 签收
            channel.basicNack(deliveryTag,true, true);
            Thread.sleep(1000);
        }

    }

}
```
<br>

