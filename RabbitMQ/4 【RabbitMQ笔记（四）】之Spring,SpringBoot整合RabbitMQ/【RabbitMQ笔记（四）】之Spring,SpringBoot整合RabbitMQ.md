## 一．	Spring整合RabbitMQ
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



## 二．	SpringBoot整合RabbitMQ生产者
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



## 三．	SpringBoot整合RabbitMQ消费者
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

















