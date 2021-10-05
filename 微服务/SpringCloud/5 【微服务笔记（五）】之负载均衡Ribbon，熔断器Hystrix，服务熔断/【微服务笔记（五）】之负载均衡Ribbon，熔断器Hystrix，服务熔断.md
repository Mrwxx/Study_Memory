## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 负载均衡Ribbon
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在实际开发中，一个服务一定有多个服务地址，当我们在服务消费方通过DiscoveryClient获取服务实例信息时，获取到的是一个服务地址列表，这时就需要负载均衡算法来决定到底访问哪一个服务。
<br>



### （二）	Ribbon
&nbsp;  &nbsp;  &nbsp;  &nbsp; Eureka 中已经集成了负载均衡组件：Ribbon，这是Netflix发布的负载均衡器，它有助于控制HTTP和TCP客户端的行为，Ribbon配置服务提供者地址列表后，Ribbon就可以基于某种负载均衡算法，自动的为服务消费者去请求，包括轮询，随机等算法。Ribbon默认的负载均衡算法时轮询，通过在服务消费方的配置文件中修改如下参数：{服务名称}.ribbon.NFLoadBalancerRuleClassName，前面是服务的名称，后面是算法的名称。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如：修改服务user-service为随机：

```yml
user-service:
	ribbon:
		NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
```
	
<br>



### （三）	实现步骤
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	要实现负载均衡，首先最起码要有两个及以上的服务提供方，创建一个8079端口，一个8080端口。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	修改服务消费方的RestTemplate实例化方法，添加负载均衡注解@LoadBalnced

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate(){
    return new RestTemplate();
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	修改服务消费方的controller，直接使用服务名称来替代原来的url中的host+port，这样Ribbon就能够自动负载均衡到一个服务地址中。

```java
String url = "http://user-service/user/" + id;
return restTemplate.getForObject(url, User.class);
```
<br>



### （四）	实现原理
&nbsp;  &nbsp;  &nbsp;  &nbsp; 为何我们只在url中提供了服务的名称就可以实现负载均衡呢？显然是有组件根据服务名称，获取到了服务实例的ip和端口号。Spring的负载均衡自动配置类LoadBalancerAutoConfiguration.LoadBalancerInterceptorConfig会自动配置负载均衡拦截器LoadBalncertInterceptor。这个类会对RestTemplate的请求进行拦截，从Eureka中根据服务的名称获取到服务列表，最后根据负载均衡算法得到真实的服务地址信息，替换url地址。访问服务地址，并且获取服务数据。

<br>



## 二. 熔断器Hystrix
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; Hystrix是一款提供保护机制的组件，由netflix公司研发。它是一个延迟和容错库，用于隔离访问远程服务，第三方库，防止出现级联失败。

<br>


### （二）	雪崩问题
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在微服务的调用过程中，一个请求可能需要调用多个微服务接口才能够实现，调用过程比较复杂。当它调用的某一个服务发生异常时，请求被阻塞了，且这个线程并不会被释放，后面请求会继续发送来一直阻塞在这里。服务器资源被耗尽，导致其他服务都不可用了，形成了雪崩效应。Hystrix解决雪崩问题的手段是服务降级，包括线程隔离和服务降级。
<br>



### （三）	线程隔离
&nbsp;  &nbsp;  &nbsp;  &nbsp; Hystrix为每个服务调用分配了一个小的线程池，如果线程池已经满调用了就立即拒绝服务调用，如果线程池中还有空闲的线程可以访问，则通过它来访问服务。
<br>



### （四）	服务降级
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当线程池已经满了，或者请求超时时，则会进行服务降级处理。所谓的服务降级指的是返回一个服务调用失败的结果，至少用户的请求不会被阻塞，更不会无休止地等待到系统崩溃。
<br>



### （五）	Hystrix实践
#### 1.	引入hystrix依赖
&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于Hystrix是作用于服务消费方消费服务的时候，因此在服务消费方的pom.xml中添加依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    <version></version>
</dependency>
```
<br>



#### 2.	开启降级
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在服务消费者的引导类上添加注解：@EnableCircuitBreaker

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，在微服务中，服务消费方引导类通常会引入三个注解：

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableCircuitBreaker
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这三个注解可以组合成一个注解：`@SpringCloudApplication`，但是本人觉得还是不要合起来写最好。
<br>



#### 3.	服务降级逻辑编写
##### （1）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当服务访问失败时，就会通过服务降级返回失败的结果，同样是在服务消费方的Controller中定义服务访问失败结果，我们需要定义一个新的方法，一个服务对应一个访问失败的方法。
<br>



##### （2）添加注解
&nbsp;  &nbsp;  &nbsp;  &nbsp; 首先，要在该服务方法上面添加注解

```xml
@HystrixCommand(fallbackMethod=”queryByIdFallBack”)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 其中的参数fallbackMethod代表着该服务对应的访问失败方法，值为方法的名称。

<br>


##### （3）定义访问失败的方法
&nbsp;  &nbsp;  &nbsp;  &nbsp; 定义这个访问失败的方法，注意，这个方法返回的一定是String，同样的，该方法对应的服务方法的返回值也要改为String，这是重点。


```java
   @GetMapping("/{id}")
    @HystrixCommand(fallbackMethod = "queryByIdFallBack")
    public String queryBuId(@PathVariable Long id){
//        String url = "http://localhost:8080/user/8";
//
//        //获取eureka中注册的user-service的实例
//        List<ServiceInstance> serviceInstances = discoveryClient.getInstances("user-service");
//        ServiceInstance serviceInstance = serviceInstances.get(0);
//        url = "http://" + serviceInstance.getHost() + ":" + serviceInstance.getPort() + "/user/" + id;
        String url = "http://user-service/user/" + id;
        return restTemplate.getForObject(url, String.class);
    }

    public String queryByIdFallBack(Long id){
        return "Sorry, the connection has failed";
    }
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，原来的服务方法已经将返回值从User改为了String！！！

&nbsp;  &nbsp;  &nbsp;  &nbsp; 此时，进行测试，当我们停止服务提供方的运行时，服务消费方访问会返回失败方法的返回值。
<br>



##### （4）为类设置默认的访问失败方法
&nbsp;  &nbsp;  &nbsp;  &nbsp; 为每个服务方法定义一个访问失败的方法是非常麻烦的，我们可以为整个类定义一个默认的访问失败的方法，作为该类中所有的服务访问失败的默认方法。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在类上面添加注解

```java
@DefaultProperties(defaultFallback=”defaultFallback”)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 同样的，defaultFallback参数表示这是默认的访问失败方法，值为方法的名称。继而定义这个方法。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在对应的服务方法上添加注解@HystrixCommand，不过这次不用添加参数

```java
public String defaultFallback(){
	return “default fail”;
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我尝试着在对应的服务方法上依旧添加注解`@HystrixCommand(fallbackMethod = "queryByIdFallBack")`，这样可以为每个服务设定个性化的访问失败方法。
<br>



#### 4.	设置Hystrix访问超时时间
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在服务消费方中配置Hystrix访问超时时间，通过以下参数：

```yml
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 3000
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这样，超时时间就设定为了3秒，当访问服务超过3秒没有响应时，那么就会启动服务降级，调用访问失败方法。我们可以通过浏览器自带的检查来查看访问该服务耗费的时间。
<br>




## 三. 服务熔断
### （一）	熔断原理
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在分布式系统中应用服务熔断后，服务调用方可以自己进行判断哪些服务反应慢或者存在大量超时，可以针对这些服务进行主动熔断，防止整个系统被拖垮。Hystrix的服务熔断机制，可以实现弹性容错，当服务请求情况好转以后，可以自动重连。通过断路的方式，将后续请求直接拒绝，一段时间（默认5秒）之后尝试地允许部分请求通过，如果这些请求调用成功则回到熔断器关闭的状态，否则继续打开熔断器，拒绝请求的服务。
<br>



### （二）	Hystrix熔断状态机模型
#### 1.	closed
&nbsp;  &nbsp;  &nbsp;  &nbsp; 熔断器关闭状态，所有的需求都能够正常访问。
<br>



#### 2.	open
&nbsp;  &nbsp;  &nbsp;  &nbsp; 熔断器打开状态，所有的服务都会被降级。Hystrix会对请求情况进行继受，当一定时间内失败请求百分比达到阈值，则触发熔断，熔断器会完全打开。默认失败比例的阈值时50%，请求次数最少不低于20次。
<br>



#### 3.	Half open
&nbsp;  &nbsp;  &nbsp;  &nbsp; 半开状态，不是永久的。熔断器打开后会进入休眠时间（默认是5s），随后熔断器会自动进入半开状态，此时会尝试地是释放部分请求，如果这些请求都能够执行成功的话，则会关闭熔断器，否则熔断器将继续保持打开，再次进行休眠计时。
<br>



### （三）	修改服务熔断的参数

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们可以在服务消费方中修改服务熔断的参数。如下所示：

```yml
hystrix:
  command:
    default:
      cirouitBreaker:
        errorThresholdPercentage: 50  #触发熔断错误比例阈值
        sleepWindowInMilliseconds: 10000 #熔断后休眠时间
        requestVolumeThreshold: 10  #熔断触发最小请求次数
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; errorThresholdPercentage: 触发熔断错误比例阈值
&nbsp;  &nbsp;  &nbsp;  &nbsp; sleepWindowInMilliseconds: 熔断后休眠的时间
&nbsp;  &nbsp;  &nbsp;  &nbsp; requestVolumeThreshold: 熔断触发最小请求次数















