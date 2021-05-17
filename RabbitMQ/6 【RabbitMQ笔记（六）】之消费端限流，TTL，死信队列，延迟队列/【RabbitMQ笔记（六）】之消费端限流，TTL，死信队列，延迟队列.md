## 一．	消费端限流
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; RabbitMQ能够削峰平谷，保障系统流量的稳定，但是若在消费端没有限制，那么消费端会有崩掉的风险，因此，我们要给消费端限流，限制每次消费端能够从Rabbitmq获取的消息数量。
<br>



### （二）消费端限流机制
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1. 设置Ack机制为手动确认，因为只有手动确认，我们才能够通过限制是否返回确认来限制消费端的流量。在监听类容器中设置属性acknowledge=”manual”
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.设置限流数量，在监听类容器中设置属性prefetch，值为想要限制的流量。

```xml
<rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" prefetch="2">
    <rabbit:listener ref="ackListener" queue-names="test_queue_confirm" ></rabbit:listener>
</rabbit:listener-container>
```

<br>



## 二．	TTL
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; TTL全称为Time To Live（存活时间），通俗意义上就是还有多长时间存活着，若到达存活时间后，该消息还没有被消费掉，那么会被自动地清除。RabbitMQ可以对消息设置TTL，也可以对整个队列设置TTL。
<br>



### （二）	队列TTL设置
&nbsp;  &nbsp;  &nbsp;  &nbsp; 队列的TTL需要在创建队列的时候就设置好，因此我们需要在生产者的Spring配置文件中为队列添加`<rabbit:queue-argumeents>`子标签，该子标签内还有`<entry>`子标签，为该子标签添加属性key=”x-message-ttl”，代表TTL，value=”10000”，代表TTL值为10000毫秒，value-type=”java.lang.Integer”代表TTL为整数。并且为该队列绑定相应的交换机。

```xml
<rabbit:queue name="test_queue_ttl" id="test_queue_ttl">
    <rabbit:queue-arguments>
        <entry key="x-message-ttl" value="10000" value-type="java.lang.Integer"></entry>
    </rabbit:queue-arguments>
</rabbit:queue>

<rabbit:topic-exchange name="test_exchange_ttl">
    <rabbit:bindings>
        <rabbit:binding pattern="ttl.#" queue="test_queue_ttl"></rabbit:binding>
    </rabbit:bindings>
</rabbit:topic-exchange>
```

<br>



### （三）	消息的TTL设置
&nbsp;  &nbsp;  &nbsp;  &nbsp; 消息的TTL设置与队列的TTL设置不同，这是在使用RabbitTemplate发送消息时设置的，它需要一个MessagePosProcessor对象，该对象为消息后处理对象，可以设置一些消息的参数信息。通常，我们要新建一个MessagePostProcessor对象，该对象有一个重写方法postProcessMessage，在方法体中我们可以设置消息的TTL。最终，将MessagePostProcessor对象传到RabbitTemplate的convertAndSend方法中。

```java
MessagePostProcessor messagePostProcessor = new MessagePostProcessor() {

        @Override
        public Message postProcessMessage(Message message) throws AmqpException {
            message.getMessageProperties().setExpiration("5000");
            return message;
        }
    };

    rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "hello_world", messagePostProcessor);
}
```

<br>



### （四）	注意事项

&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	如果同时设置了队列的TTL和消息的TTL，则以时间短的为准。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	队列过期后，会将队列上所有的消息全部移除。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	消息过期后，只有当消息在队列顶端时，才会被判断是否过期，然后被移除。如果该消息并不在队列顶端，即使过期也不会被移除。


<br>



## 三．	死信队列
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 死信队列（DLX），全称为Dead Letter Exchange，当消息成为死信（Dead Message）后，可以被重新发送到另一个交换机中，这个交换机就是DLX，死信交换机可以将死信发送到死信队列中，被其他的消费者进行消费。
<br>



### （二）	称为死信的情况
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	队列消息长度到达限制，多出来的消息成为死信。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	消费者拒绝消费消息，即消息处理失败，通道调用basicNack/basicReject函数，并且不把消息重新放回到原目标队列中，requeue=false，该消息成为死信。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	原队列存在消息过期设置，消息到达存活时间未被消费掉，成为死信。

<br>


### （三）	队列绑定死信交换机
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当正常的队列中存在死信时，若要将死信发送到死信交换机中，需要为该队列绑定死信交换机，为该队列设置参数 x-dead-letter-exchange，值为死信交换机的名称，设置参数x-dead-letter-routing-key，该参数为正常队列发送到死信交换机的routing-key。
<br>



### （四）	代码实现
#### 1.	声明正常队列和正常交换机

```xml
<rabbit:queue name="test_queue_dlx" id="test_queue_dlx"></rabbit:queue>
<rabbit:topic-exchange name="test_exchange_dlx">
    <rabbit:bindings>
        <rabbit:binding pattern="test.dlx.#" queue="test_queue_dlx"></rabbit:binding>
    </rabbit:bindings>
</rabbit:topic-exchange>
```
<br>




#### 2.	声明死信队列和死信交换机
  &nbsp;  &nbsp;  &nbsp;  &nbsp;  死信队列和死信交换机和正常的没有区别。

```xml
<rabbit:queue name="queue_dlx" id="queue_dlx"></rabbit:queue>
<rabbit:topic-exchange name="exchange_dlx">
    <rabbit:bindings>
        <rabbit:binding pattern="dlx.#" queue="queue_dlx"></rabbit:binding>
    </rabbit:bindings>
</rabbit:topic-exchange>
```

<br>



#### 3.	正常队列绑定死信交换机
&nbsp;  &nbsp;  &nbsp;  &nbsp; 为正常队列设置参数来绑定死信交换机。这些参数的名称可以在rabbitmq的管理控制台的queue页面中通过arguments参数获取到。

```xml
<rabbit:queue name="test_queue_dlx" id="test_queue_dlx">
    <rabbit:queue-arguments>
        <entry key="x-dead-letter-exchange" value="exchange_dlx"/>
        <entry key="x-dead-letter-routing-key" value="dlx.h"/>
    </rabbit:queue-arguments>
</rabbit:queue>
```
<br>



#### 4.	设置死信队列的产生条件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 产生死信队列是有产生条件的，要么是队列过期，要么是队列长度限制。我们可以在正常queue的定义中设置这些条件，来产生死信队列。

```xml
<entry key="x-message-ttl" value="10000" value-type="java.lang.Integer"/>
<entry key="x-max-length" value="10" value-type="java.lang.Integer"/>
```
<br>



#### 5.	测试
##### （1）	测试队列过期消息称为死信
&nbsp;  &nbsp;  &nbsp;  &nbsp; 发送一条正常的消息给正常的交换机和队列，过了TTL后它就会称为死信并且发送给死信交换机和队列。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring-rabbitmq-producer.xml")
public class DeadTest {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void test(){
        //测试TTL
        rabbitTemplate.convertAndSend("test_exchange_dlx", "test.dlx.ddd", "Dead Msg");
    }
}
```
<br>



##### （2）	测试队列长度限制，多余的消息称为死信
&nbsp;  &nbsp;  &nbsp;  &nbsp; 只要发送大于队列长度的消息，多出来的消息就会成为死信发送到死信队列中。

```java
for(int i = 0; i < 20; ++i){
    rabbitTemplate.convertAndSend("test_exchange_dlx", "test.dlx.e", "ddd");
}
```
<br>



##### （3）	测试消费者拒绝接受消息，消息称为死信
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当消费者处理消息失败时，会拒绝接受这条消息，且没有设置重新发回到原有的队列中，该消息就会成为死信，发送到死信队列中。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在消费者处理消息的异常代码段，将通道的basicNack函数的requeue参数设置为false；同时在Spring配置文件中配置监听器时持续监听正常队列即可，拒收的消息会被该正常队列发送到死信交换机和队列中。
<br>




## 四．	延迟队列
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 延迟队列，即消息进入队列后不会被立即消费，而是在到达指定的时间后，才会被消费掉。这种队列在许多场景中可以应用，如下单未支付的倒计时，只要是与计时有关的场景，都可以使用延迟队列实现。
<br>



### （二）	Rabbitmq中的延迟队列
&nbsp;  &nbsp;  &nbsp;  &nbsp; Rabbitmq中没有延迟队列，但是我们可以通过TTL + 死信队列的方式来实现延迟队列。即首先设置队列的TTL为指定的时间，过了该时间后，死信被发送给死信交换机，继而发送给死信队列，最终到达消费者，这就实现了延迟队列的功能。

<br>
