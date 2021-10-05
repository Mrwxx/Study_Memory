## 一．	Spring Cloud Config 分布式配置中心
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;在分布式系统中，由于服务数量众多，每个微服务项目中都有自己的配置文件，无法统一地管理所有的配置文件。这个问题可以通过分布式配置中心组件来解决，在Spring Cloud中提供了Spring Cloud Config组件，支持配置文件放在配置服务的本地，也可以放在远程Git仓库中，这样，我们直接使用Git来管理配置文件即可。

&nbsp;  &nbsp;  &nbsp;  &nbsp;当然，这个配置中心组件也是一个微服务，需要注册在Eureka注册中心中。
<br>



### （二）	Git配置管理
#### 1.	创建gitee仓库
&nbsp;  &nbsp;  &nbsp;  &nbsp;在码云平台中创建一个仓库。
<br>



#### 2.	在仓库中创建配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;配置中心组件是为了在Git上管理配置文件的，因此，我们需要在仓库中创建配置文件。这个配置文件的命名是由规范的，由两部分组成，中间中“-“分隔开。前面一部分是应用名称，后面一部分是用于区分开发环境，测试环境，生产环境的标识。配置文件格式依然是yml和properties两种。如下所示：

&nbsp;  &nbsp;  &nbsp;  &nbsp;user-dev.yml表示应用名为user，在开发环境下使用的配置文件。

&nbsp;  &nbsp;  &nbsp;  &nbsp;将user-service服务的配置文件作为内容在仓库中创建新的配置文件user-dev.yml。

```java
server:
  port: ${port:8080}
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/springcloud?autoReconnect=true&useSSL=false
    username: root
    password: 123
  application:
    name: user-service

mybatis:
  type-aliases-package: com.user.pojo
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8082/eureka
  instance:
    ip-address: 127.0.0.1
    prefer-ip-address: true
    lease-expiration-duration-in-seconds: 5
    lease-renewal-interval-in-seconds: 5
```
<br>



#### 3.	创建配置中心工程
##### （1）	配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp;添加eureka-client 注册中心客户端依赖，config-server配置中心依赖。

```java
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>
</dependencies>
```
<br>



##### （2）	添加引导类
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建引导类，并且添加注解@EnalbeConfigServer表示开启配置服务。

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApp {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApp.class, args);
    }
}
```
<br>



##### （3）	创建配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;在配置文件中添加端口，服务名称，gitee地址，以及注册到eureka服务器。

```java
server:
  port: 8085

spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/Mrwxxx/config-test.git

eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8082/eureka
```
<br>



##### （4）	测试配置中心
&nbsp;  &nbsp;  &nbsp;  &nbsp;访问配置中心的地址，它会直接连接到Gitee的仓库地址中，可以直接访问到仓库中创建的配置文件user-dev,yml。同时，在Gitee中修改配置文件也能够即时地更新到配置中心工程中来。
<br>



### （三）	其他工程获取配置中心中的配置
#### 1.	需求分析
&nbsp;  &nbsp;  &nbsp;  &nbsp;将用户微服务工程user-service的配置文件application.yml删除，改从配置中心config-server中获取。
<br>



#### 2.	实现步骤
##### （1）	在user-service工程中添加config的启动器依赖。

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
    <version>2.1.1.RELEASE</version>
</dependency>
```
<br>



##### （2）	修改配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除之前的application.yml文件，而添加一个bootstrap.yml，这个配置文件专用于配置固定的参数，动态的参数要设置在Gitee仓库中。

&nbsp;  &nbsp;  &nbsp;  &nbsp;bootstrap.yml中需要配置Spring Cloud config配置中心的参数，如这个用户微服务工程所对应Gitee中的配置文件user-dev.yml，设置name参数为user，设置profile参数为dev，设置label参数为user-dev.yml在Gitee中所处的分支master，同时还有discovery中要开启发现配置中心，以及设置配置中心的服务名config-server。注意，别忘了将该服务注册到Eureka上面。

```java
spring:
  cloud:
    config:
      name: user
      profile: dev
      label: master
      discovery:
        enabled: true
        service-id: config-server

eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8082/eureka
```
<br>



##### （3）	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;启动用户微服务，发现访问依旧是正常的，说明用户微服务能够直接从配置中心中拉取对应的配置文件，，测试成功！！！

<br>



## 二．	Spring Cloud Bus服务总线
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们在Gitee中修改配置的文件后，无法自动更新到项目之中，只有重启项目更新的配置才会生效。那么，如何在不重启微服务的情况下自动更新配置呢？这就需要使用到Spring Cloud Bus了。Spring Cloud Bus是用轻量的消息代理将分布式的节点连接起来，可以用于广播配置文件的更改或者服务的监控管理，也可以实现应用程序之间互相通信。Spring Cloud Bus可选的消息代理有RabbitMQ和Kafka。

<br>


### （二）	原理
&nbsp;  &nbsp;  &nbsp;  &nbsp;配置中心从Git仓库中拉取最新的配置文件，缓存到本地中，各个微服务从本地配置缓存中获取配置文件，并且注册到注册中心Eureka之中。在使用了Spring Cloud Bus总线之后，配置中心中的配置检测到修改后，发送消息到RabbitMQ之中，各个微服务实时监控着RabbitMQ的消息情况，只要有配置文件修改的消息，则通过配置中心重新拉取新的配置文件，实现实时更新功能。
<br>



### （三）	Spring Cloud Bus实例
#### 1.	需求分析
&nbsp;  &nbsp;  &nbsp;  &nbsp;启动RabbitMQ通过修改码云中的配置文件后发送Post请求实现及时更新用户微服务中的配置项。
<br>



#### 2.	启动RabbitMQ
&nbsp;  &nbsp;  &nbsp;  &nbsp;之前已经在Linux中安装过RabbitMQ，直接启动RabbitMQ。
<br>



#### 3.	修改配置中心
##### （1）	修改依赖
&nbsp;  &nbsp;  &nbsp;  &nbsp;添加Spring Cloud Bus需要的依赖。如下所示：

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-bus</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-stream-binder-rabbit</artifactId>
</dependency>
```
<br>



##### （2）	在配置中心中配置rabbitmq
&nbsp;  &nbsp;  &nbsp;  &nbsp;配置中心需要向rabbitmq发送消息，因此需要在配置中心中配置rabbitmq的参数，如host,port,username,password。

```java
spring:
	rabbitmq:
		host: 192.168…
		port: 5672
		username: wxx
		password: wxx
```

<br>



##### （3）	在配置中心中配置暴露触发消息总线的地址
&nbsp;  &nbsp;  &nbsp;  &nbsp;配置该地址后就能通过访问该地址来触发消息总线，更新配置文件。

```java
management:
  endpoints:
    web:
      exposure:
        include: bus-refresh
```
<br>



##### （4）	配置服务提供者的依赖
&nbsp;  &nbsp;  &nbsp;  &nbsp;服务提供者需要监听Rabbitmq，因此需要添加Spring Cloud Bus总线的依赖。

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-bus</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-stream-binder-rabbit</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
<br>



##### （5）	在服务提供者中配置rabbitMQ
&nbsp;  &nbsp;  &nbsp;  &nbsp;服务提供者需要监听rabbitMQ，因此需要在配置文件中配置rabbitMQ的参数。与之前的配置相同。

```java
spring:
	rabbitmq:
		host: 192.168…
		port: 5672
		username: wxx
		password: wxx
```
<br>



##### （6）	修改服务提供者的Controller
&nbsp;  &nbsp;  &nbsp;  &nbsp;服务提供者的哪个Controller需要实时更新，就在哪个Controller之上添加注解@RefreshScope，表示实时刷新配置。
<br>



##### （7）	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，在启动各个工程时，配置中心要在其他服务之前启动，因为其他服务的配置文件在配置中心上。当我们在Gitee上修改配置文件后，直接访问服务提供者发现配置文件是没有更新的，通过RESTClient工具发送POST方式请求访问http:127.0.0.1:8085/actuator/bus-refresh ，8085是配置中心的端口号，actuator是固定的，bus-refresh是我们在配置中心添加的触发消息总线的地址。访问完该地址后，配置文件就会自动更新。

<br>



## 三．	Spring Cloud 体系综合应用
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring Cloud 应用逻辑图，如下所示：



 


![在这里插入图片描述](https://img-blog.csdnimg.cn/20201109213006601.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70#pic_center)






