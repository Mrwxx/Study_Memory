## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．搭建Eureka注册中心工程
### （一）配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 依然是继承之前设置的父工程SpringCloudParentDemo，然后添加eureka-server的启动依赖，这是netflix公司的，版本不需要设置，因为在父工程中已经添加了spring-cloud依赖了。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.springboot</groupId>
    <artifactId>eureka-server</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <artifactId>com.SpringCloud.ParentDemo</artifactId>
        <groupId>com.SpringCloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>


</project>
```
<br>



### （二）创建引导类和配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建引导类，并且添加注解@EnableEurekaServer，表示这是一个Eureka服务器。

```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaApp {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApp.class, args);
    }
}
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 添加配置文件application.yml，配置文件application.yml配置Eureka服务器的参数：

```yml
server:
  port: 8082
spring:
  application:
    name: eureka-server
eureka:
  client:
    service-url: 
      #eureka服务地址，如果是集群的话，需要制定其他集群eureka的地址
      defaultZone: http:127.0.0.1/8082/eureka
    #单个服务器不注册自己到Eureka中
    register-with-eureka: false
    #不拉取服务
    fetch-registry: false
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; spring.application.name是Eureka服务的名称。

&nbsp;  &nbsp;  &nbsp;  &nbsp; eureka.client.service-url 是eureka服务的地址，当有多个服务器成集群时，需要指定其他集群eureka的地址

&nbsp;  &nbsp;  &nbsp;  &nbsp; eureka.client.register-with-eureka：是否注册Eureka本身到Eureka中，在单个服务器中是无所谓的，当呈集群存在时，需要注册Eureka本身到Eureka中被其他Eureka发现。

&nbsp;  &nbsp;  &nbsp;  &nbsp; eureka.client.fetch-registry:是否需要拉取服务。
<br>



### （三）服务的注册与发现
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们需要将服务提供方的服务注册到eureka并且在服务消费方能够根据服务名称调用服务。
#### 1.	服务注册
&nbsp;  &nbsp;  &nbsp;  &nbsp;  （1）	在服务提供方添加Eureka客户端依赖，自动将服务注册到EurekaServer服务地址列表中。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;  （2）	改造引导类，添加开启Eureka客户端发现的注解。


```xml
@EnableEurekaClient
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 该注解用于开启Eureka客户端的发现功能。
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; （3）	修改配置文件，添加服务提供方的应用名称，设置Eureka服务地址。

```xml
spring.application.name=user-service
eureka.client.service-url.defaultZone=http://127.0.0.1:8082/eureka
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;  （4）	启动测试
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在Eureka服务器已经开启的情况下，开启服务提供方，那么服务提供方就会出现在Eureka的管理界面上。

<br>



#### 2.	服务发现
&nbsp;  &nbsp;  &nbsp;  &nbsp;  （1）	在服务消费方添加Eureka客户端依赖，可以使用工具类根据服务名称获取对应的服务地址列表。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; （2）	改造引导类，添加开启Eureka客户端发现的注解。

```xml
@EnableEurekaClient
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 该注解用于开启Eureka客户端的发现功能。
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; （3）	修改配置文件，设置Eureka服务地址，并且设置服务消费方的名称。

```yml
server:
  port: 8081

eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8082/eureka
spring:
  application:
    name: consumer-demo
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; （4）	改造Controller，使用工具类DiscoveryClient根据服务名称获取的对应服务地址列表。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 原来的Controller中服务提供方的地址是直接写死在代码中的，这样不利于扩展，我们可以注入一个工具类DiscoveryClient，该工具类可以获取Eureka服务器中注册的服务实例，通过该实例可以过去该实例的主机，以及端口号，以此可以拼接成该服务的地址。再通过RestTemplate的getForObject函数来访问该地址，得到服务提供的数据。

```java
@RestController
@RequestMapping("/consumer")
public class ConsumerController {
    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    private DiscoveryClient discoveryClient;

    @GetMapping("/{id}")
    public User queryBuId(@PathVariable Long id){
        String url = "http://localhost:8080/user/8";

        //获取eureka中注册的user-service的实例
        List<ServiceInstance> serviceInstances = discoveryClient.getInstances("user-service");
        ServiceInstance serviceInstance = serviceInstances.get(0);
        url = "http://" + serviceInstance.getHost() + ":" + serviceInstance.getPort() + "/user/" + id;
        return restTemplate.getForObject(url, User.class);
    }
}
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; （5）	测试服务消费方
&nbsp;  &nbsp;  &nbsp;  &nbsp; 开启服务消费方，可以得到服务提供方提供的服务数据，并且服务提供方和服务消费方都已经在Eureka管理界面中了。
<br>




## 二．高可用的Eureka Server
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在之前的案例中，我们搭建了一个EurekaServer，而EurekaServer也可以是一个集群，形成高可用的Eureka中心。所谓的高可用，就是保证当一个Eureka Server宕机后，另一个Eureka Server 能够立即补上去，这就需要多个Eureka Server之间需要数据同步。
<br>



### （二）服务同步
&nbsp;  &nbsp;  &nbsp;  &nbsp; 多个Eureka Server之间能够互相注册为服务，集群中的每个Eureka Server 需要将自己注册到其他Eureka Server 服务之中，这样其他的Eureka Server就能够互相发现。当服务提供者注册到Eureka Server集群中的某个节点时，该节点会把服务的信息同步给集群中的每个节点，从而实现数据同步。因此，无论客户端访问到Eureka Server集群中的任意一个节点，都能够获取到完整的服务列表信息。
<br>



### （三）配置高可用的Eureka Server
&nbsp;  &nbsp;  &nbsp;  &nbsp; 直接修改之前配置的Eureka Server，之前配置的Eureka 的服务地址使用的是自己的Eureka Server地址，现在我们要配置集群，需要修改为其他服务器的地址。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 需要将自己注册到Eureka 中，设置register-with-eureka，默认是true。
<br>




```yml
server:
  port: ${port:8082}
spring:
  application:
    name: eureka-server
eureka:
  client:
    service-url:
      #eureka服务地址，如果是集群的话，需要制定其他集群eureka的地址
      defaultZone: ${defaultZone:http://127.0.0.1/8083/eureka}
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 这里，我们使用一种新的语法：

```xml
port: ${port:8082}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这种语法的意思是如果port中有值的话，就使用port中的值，否则使用后面的值。要使用这种语法，需要设置这些参数的默认值，在IDEA的右上角选择Eureka 项目的配置，在Configuration 的Environment中，设置VM options为使用的参数的默认值，使用-D表示这是默认值。如下所示：

```xml
-Dport=8082 -DdefaultZone=http://127.0.0.1:8083/eureka
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，这里设置的port默认值为8082，这是本台Eureka Server的端口号，defaultZone的默认值为其他Eureka Server 的地址，即如果有集群，则注册其他的Eureka Server的地址到本台Eureka Server中。
<br>



### （四）复制配置好的Eureka Server应用
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同样是在Eureka Server的应用配置页面，直接复制该应用，得到另一个Eureka Server，修改名称，并且修改参数的默认值，将port的默认值改为新的端口号8083，将defaultZone的默认值改为http://127.0.0.1:8082/eureka。
<br>



### （五）	测试高可用集群
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同时启动两个Eureka Server应用，可以在两个的Eureka 管理界面中看到，每个都已经注册了两个Eureka Server了，一个是8082端口，一个是8083端口。设置服务提供方和服务消费方中配置的都是8082端口的服务器，当我们停掉8082端口的服务器时，发现服务提供方和服务消费方依旧能够运行。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在配置服务提供者和服务消费方时，为了保证稳定，我们可以将多个Eureka Server的地址都配置到其中。

<br>



## 三．Eureka客户端与服务端配置
### （一）Eureka服务提供方客户端配置
#### 1. 服务注册
&nbsp;  &nbsp;  &nbsp;  &nbsp; 服务提供者在启动时，会检测配置属性中的 eureka.client.register-with-eruka-true参数是否正确，默认是true。如果值是true，那么服务提供者就会向Eureka Server发起一个Rest请求，并携带自己的元数据信息，Eureka Server会把这些信息保存到一个双层Map结构中。双层Map结构如下所示：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 第一层Map的Key是服务id。根据我们配置的spring.application.name属性来获取。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 第二层Map的Key是服务的实例id，一般是host+serviced+port.

&nbsp;  &nbsp;  &nbsp;  &nbsp; 值是服务的实例对象，一个服务可以启动多个不同的实例，形成集群。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 且，在默认注册时是使用的是主机名或者localhost，如果想用ip进行注册，需要在服务提供者的配置中手动添加ip地址，并且设置倾向ip地址：

```yml
eureka：
	instance:
		ip-address: 127.0.0.1
		prefer-ip-address：true
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，这样设置之后，只是在代码中变成了ip地址，然而Eureka的控制台中依然是localhost。
<br>



#### 2．服务续约
&nbsp;  &nbsp;  &nbsp;  &nbsp; 注册服务以后，服务提供者会持续地提供一个心跳，即定时向Eureka Server 发起Rest请求，表示该服务依旧存在，这种操作称为服务续约。服务提供方默认的定时续约的间隔时间为30秒，同时默认的服务失效的等待心跳时间为90秒，即若服务提供方90秒内没有心跳，则该服务就会失效，但是服务何时从Eureka Server服务列表中剔除出来，就要看Eureka Server定时扫描时间的设置了，只有Eureka Server定时扫描发现服务没有发送心跳时，他才会从服务列表中剔除。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置服务续约的参数，如下所示：

```yml
eureka:
	instance:
		#服务失效
		lease-expiration-duration-in-seconds: 90
		#服务续约
		leas-renewal-interval-in-seconds: 30
```

<br>



### （二）	Eureka服务消费方客户端配置
#### 1.	获取服务列表
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当服务消费方启动时，会检测Eureka.client.fetch-registry参数的值，如果是true，那么会从Eureka Server的服务列表中拉取只读备份，缓存在本地中，每隔30秒重新拉取更新。在服务消费方的配置中修改如下参数：

```yml
eureka:
		client:
			registry-fetch-interval-seconds: 30
```
<br>



### （三）	Eureka Server服务端配置
#### 1.	服务下线
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当服务正常关闭时，它会触发一个服务下线的Rest请求给Eureka Server，服务器接收到该请求后将服务设置为下线状态。
<br>



#### 2.	失效剔除
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当Eureka Server没有收到服务下线的请求，同时Eureka Server有一个定时扫描任务，默认是60秒扫描一次当前服务清单中超时未续约的服务，并剔除出服务列表。可以通过eureka.server.eviction-interval-timer-in-ms 参数默认的时间，单位是毫秒。

```yml
eureka:
	server:
		eviction-interval-timer-in-ms: 60000
```
<br>



#### 3.	自我保护
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当我们关停一个服务时，Eureka Server的控制台中会有一个警告，这是Eureka的自我为保护机制，当服务没有按时续约时，Eureka会统计该服务实例 最近15分钟续约的比例是否低于85%。由于网络的波动，服务未续约的情况时有发生，因此Eureka Server在扫描到有未续约的服务时，不会直接删除，而是等待网络恢复正常。这种保护机制保障了服务的稳定。在生产环境中比较实用，但是在开发过程中我们会频繁地打开关闭服务，因此，最好将自我保护机制关掉。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以通过在Eureka Server中设置如下参数关闭自我保护：

```yml
eureka:
		server:
			enable-self-preservation: false
```

<br>



