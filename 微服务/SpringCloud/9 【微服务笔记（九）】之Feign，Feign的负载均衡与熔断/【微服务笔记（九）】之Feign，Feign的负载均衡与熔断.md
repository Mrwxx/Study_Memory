## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Feign
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;之前使用Ribbon的负载均衡功能，简化了远程调用时的代码，但是每次调用都需要写基本相同的代码，代码重复性高。Feign可以把Rest的请求进行隐藏，伪装成类似Controller一样，我们不需要拼接url，这些工作都可以让Feign来完成。
<br>


### （二）	Feign实例
#### 1.	在服务消费方添加依赖

```cpp
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```
<br>



#### 2.	开启Feign功能
&nbsp;  &nbsp;  &nbsp;  &nbsp;在服务消费方的引导类上添加注解@EnableFeignClients，用于开启Feign功能。
<br>



#### 3.	创建Feign客户端
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个Feign客户端接口，在该接口上添加注解@FeignClient(“user-service”)，该注解声明了当前的类是一个Feign客户端，并且指定了访问的服务名是user-service。Feign客户端就是为了拼接URL，接口内的每个方法都是一个请求方法，在每个方法上添加注解访问的URL，如@GetMapping(“/user/{id}”)，动态代理会自动生成该接口的实现类，将该接口的服务名和请求方法的URL拼接起来，组成最终访问提供服务的URL.。

```java
package com.springboot.client;

import com.springboot.pojo.User;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient("user-service")
public interface ConsumerFeignClient {

    @GetMapping("/user/{id}")
    User queryById(@PathVariable Long id);
}
```
<br>



#### 4.	创建处理器，注入Feign客户端
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个处理器Controller，注入Feign客户端，使用该客户端接口的方法queryById，动态代理会自动创建该接口的实现类。那么该方法实现的就是访问客户端拼接成的服务地址，返回服务数据。

```java
@RestController
@RequestMapping("feign")
public class ConsumerFeignController {

    @Autowired
    private ConsumerFeignClient feignClient;

    @GetMapping("/{id}")
    public User queryById(@PathVariable Long id){
        return feignClient.queryById(id);
    }
}
```
<br>



#### 5.	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;启动服务提供方，Eureka Server，服务消费方，通过Feign的Controller设定的地址访问其中的方法，就可以获取相应的服务数据。

<br>



## 二．	Feign的负载均衡和熔断
### （一）	Feign配置Ribbon
&nbsp;  &nbsp;  &nbsp;  &nbsp;Feign中已经集成了Ribbon依赖和自动配置，因此不需要额外引入依赖，也不需要再注册RestTemplate对象。Feign内置的Ribbon默认设置了请求超时时长，默认是1000，我们可以通过手动配置来修改：

```java
ribbon:
	ReadTimeout: 2000 #读取超时时长
	ConnectionTimeout: 1000 #建立连接的超时时长
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;Ribbon内部有重试机制，超时后会自动重新发起请求，如果想修改，如下所示：

```java
ribbon:
	ConnectionTimeout: 1000 #连接超时时长
	ReadTimeout: 2000 #数据通信超时时长
	MaxAutoRetries: 0 #当前服务器的最大重试次数
	MaxAutoRetriesNextServer: 0 
	OkToRetryOnAllOperations: false #是否对所有的请求方式都重试
```
<br>



### （二）	Feign配置Hystrix
#### 1.	开启Hystrix
&nbsp;  &nbsp;  &nbsp;  &nbsp;同样的，Feign中已经内置了Hystrix，直接通过配置来开启Hystrix，如下所示：

```java
feign:
	hsytrix:
		enabled: true
```
<br>



#### 2.	创建服务降级的类
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个在服务降级时调用的类，该类中的每个方法都与Feign客户端接口中的方法对应，因此直接实现Feign客户端接口，重写每个方法，编写访问服务失败时的操作。注意，要在该类之上添加注解`@Component`，让Spring知道这是一个组件。

```java
@Component
public class FeignClientFallback implements ConsumerFeignClient {
    @Override
    public User queryById(Long id) {
        User user = new User();
        user.setId(id);
        user.setName("error");
        return user;
    }
}
```
<br>



#### 3.	修改Feign客户端的@FeignClient注解
&nbsp;  &nbsp;  &nbsp;  &nbsp;修改Feign客户端的`@FeignClient`注解，添加fallback参数，绑定服务降级类到Feign客户端中。

```java
@FeignClient(value="user-service", fallback = FeignClientFallback.class)
```
<br>



#### 4.	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;设定Hystrix的超时时间为2秒，同时在Feign的Controller中设置休眠两秒，制造异常，发现服务降级成功。
<br>



### （三）	Feign配置压缩
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring Cloud Feign能够对请求和响应进行GZIP压缩，以减少通信过程中的性能损耗。直接通过配置feign中的参数即可开启压缩功能，如下所示：

```java
feign:
	compression:
		request:
			enabled: true #请求压缩
			mime-types: text/html,application/xml,application.json #压缩的数据类型
			mim-request-size: 2048 #设置触发压缩的大小下限
		response:
			enabled: true #响应压缩
```
<br>



### （四）	Feign配置日志级别
&nbsp;  &nbsp;  &nbsp;  &nbsp;一般的包的日志级别可以通过在配置文件中设置 logging.level.xx=debug,就可以为xx这个包设定日志级别为debug。然而对于被@FeignClient注解修改过的Feign客户端接口来说，当它被代理时，会创建一个新的Feign.Logger实例，我们需要额外指定这个日志的级别。需要创建一个Feign的配置类，用于创建新的Logger实例。

<br>


#### 1.	修改包的日志级别

```java
logging:
		level:
			com.springboot: debug
```
<br>



#### 2.	添加Feign的配置类
&nbsp;  &nbsp;  &nbsp;  &nbsp;添加Feign的配置类，定义日志级别。注意，要在该配置类上添加@Configuration表示这是一个配置类。

```java
package com.springboot.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}
```
<br>



Feign支持的日志级别有4种：
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	NONE： 不记录任何日志信息，默认

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	BASIC：  只记录请求的方法，URL以及响应状态码和执行时间

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	HEADERS：在BASIC的基础上，添加了请求和响应的头信息

&nbsp;  &nbsp;  &nbsp;  &nbsp;（4）	FULL：记录所有请求和响应的明细，包括头信息，请求体，元数据
<br>



#### 3.	在Feign的客户端中指定配置类
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个日志的配置类是为Feign客户端接口配置的，因此需要在Feign的客户端接口中通过注解`@FeignClient`指定该配置类。

```java
@FeignClient(value="user-service", fallback = FeignClientFallback.class, configuration = FeignConfig.class)
```
<br>



#### 4.	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;开启服务消费方，正常访问后，日志就会打印在控制台中。
<br>

