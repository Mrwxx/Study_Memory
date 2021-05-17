## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Spring Cloud Gateway网关
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring Cloud Gateway是Spring自己开发的网关服务，基于Filter链提供网关基本功能：安全，监控，限流，能够为微服务架构提供简单，有效且统一的API路由管理。
<br>



### （二）	原理
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring Cloud Gateway组件的核心是过滤器，通过这些过滤器可以将客户端发送的请求路由到对应的微服务。Spring Cloud Gateway是设置在微服务前端的防火墙和代理器，能够隐藏微服务节点的ip端口信息，从而加强安全保护。一切来自客户端的请求或者是服务内部调用，只要是对服务的请求都可以经过网关，然后通过网关来验证，路由。
<br>



### （三）	概念
#### 1.	路由(route)
&nbsp;  &nbsp;  &nbsp;  &nbsp;路由信息由一个ID，一个目的URL，一组断言工厂，一组Filter组成。如果路由断言为真，说明请求URL和配置路由匹配。
<br>



#### 2.	断言（Predicate）
&nbsp;  &nbsp;  &nbsp;  &nbsp;断言函数允许开发者自定义匹配来气Http Request中的任何信息。
<br>



#### 3.	过滤器（Filter）
&nbsp;  &nbsp;  &nbsp;  &nbsp;Filter分为两种，一种是Gateway Filter, 一种是Global Filter，过滤器会对请求和响应进行修改处理。
<br>



### （四）	Gateway项目实战
#### 1.	需求分析
&nbsp;  &nbsp;  &nbsp;  &nbsp;需求是将包含有/user的URL请求路由到http://localhost:8080/user/id中
<br>



#### 2.	创建工程
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个Maven工程springcloud-gateway。
<br>



#### 3.	添加依赖
&nbsp;  &nbsp;  &nbsp;  &nbsp;需要添加两个依赖，一个是gateway的，还有一个是Eureka client的，因为gateway本身也是一个服务，需要注册到Eureka Server中。

```java
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        <version></version>
    </dependency>
</dependencies>
```
<br>



#### 4.	创建引导类即配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;在引导类上添加`@EnableDiscoveryClient`注解表示能够发现Eureka Server，并注册到Eureka Server中。

```java
package com.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class GatewayApp {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApp.class, args);
    }
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;设置application.yml，基础的设置不用过多说明，主要是网关gateway的配置。在gateway的routes下有多个路由，每个路由表示一种路由方式。id表示路由的id，可以任意编写；uri表示要代理的服务地址，即路由到的服务地址；predicates表示断言，可以有多个断言，断言即判断请求是否满足断言的条件，如Path路径中含有/user时就进行路由。

```java
server:
  port: 8084
spring:
  application:
    name: api-gateway
  cloud:
    gateway:
      routes:
          #路由id
        - id: user-service-route
          #代理的服务地址
          uri: http://127.0.0.1:8080
          #路由断言
          predicates:
            - Path=/user/**
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:8082/eureka
  instance:
    prefer-ip-address: true
```
<br>



#### 5.	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过访问网关的端口8084，并且在路径中添加/user/7，符合路由的断言，那么该请求就会被路由到8080的服务地址，并且路径中的/user/7同样被添加到访问服务的请求URL中，访问成功。

<br>



## 二．	面向服务的路由
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;当同一个服务有多个实例的话，需要使用动态路由来从网关路由到相应的服务地址，这就需要修改之前设置的网关的uri，使用服务的服务名称来动态路由。当uri使用的协议为lb时，网关将使用LoadBalncerClient把user-service通过eureka解析为实际的主机和端口，并进行Ribbon负载均衡。
<br>



### （二）	修改网关配置
&nbsp;  &nbsp;  &nbsp;  &nbsp;修改网关的配置文件，将路由的uri修改为 lb://服务名称 这种格式。

```java
uri: lb://user-service
```

<br>



## 三．	路由前缀处理
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;用户发送请求到网关时，不一定是正确的请求地址，可能会有所偏差，这时就需要我们来为用户修正请求地址了。
<br>



### （二）	添加前缀
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过配置路由的过滤器PrefixPath，实现映射路径中地址的添加。添加如下参数：

```java
Spring:
cloud:
	gateway:
		routes:
-	id: user-service-route
uri: lb://user-service
predicates:
		-Path=/**
			filters:
				-PrefixPath=/user
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;上面的配置中，断言的路径是所有路径，它将所有的路径都放进来，但是在过滤器中添加了路径的前缀 /user，即为所有进来的请求路径中都添加了/user 前缀，如请求为localhost:8084/7，进来以后就变为了localhost:8084/user/7。
<br>



### （三）	去除前缀
&nbsp;  &nbsp;  &nbsp;  &nbsp;同理，通过配置StripPrefix，可以将映射路径中地址的前缀去除。StipPrefix的值为要去掉前缀的个数，以”/”为分割线。如请求路径为/api/user/**，那么当StripPrefix为1时，就去掉/api，当StipPrefix为2时，就去掉/api/user。


```java
Spring:
	cloud:
		gateway:
			routes:
				-id: user-service-route
				uri: lb://user-service
				predicates:
					-Path=/api/user/**
				filters:
					-StripPrefix=1
```

<br>

## 四．	过滤器
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;过滤器为网关实现请求的鉴权功能，Gateway自带了几十个过滤器，能够为用户的请求修改很多参数。如：AddRequestHeader能够对匹配上的请求添加Header，AddRequestParematers能够为匹配上的请求路由添加参数，AddResponseHeader能够为从网关上返回的响应添加Header。
<br>



### （二）	AddResponseHeader过滤器示例
&nbsp;  &nbsp;  &nbsp;  &nbsp;在网关的配置文件中添加过滤器的配置，本次添加的是全局的默认过滤器，对所有路由都有效，如下所示：

```java
spring:
		cloud:
			gateway:
				routes:
					…
				default-filters:
-AddResponseHeader=X-Response-Foo, Bar
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;过滤器的值为两个，第一个是要添加的参数名，第二个是该参数的值。
<br>



### （三）	过滤器类型
#### 1.	局部过滤器
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过spring.cloud.gateway.routes.filters 配置在具体的路由下面，只作用在当前路由上，自带的过滤器都可以配置或者自定义的过滤器。像我们上面配置spring.cloud.gateway.default-filters可以对所有的路由生效，这是一种全局的过滤器，但是这些过滤器的实现都是要实现GatewayFilterFactory接口的。
<br>



#### 2.	全局过滤器
&nbsp;  &nbsp;  &nbsp;  &nbsp;全局过滤器不需要再配置文件中配置，作用在所有的路由中，实现GlobalFilter接口即可。
<br>



### （四）	过滤器执行生命周期
&nbsp;  &nbsp;  &nbsp;  &nbsp;在过滤器执行的前后可以执行一些操作，即通过过滤器的GatewayFilterChain执行filter方法的前后来实现。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如，请求鉴权时，在执行filter方法之前，进行鉴定访问权限，执行过滤器后，如果没有权限，就返回空。同样的，在异常处理时，执行filter方法后，记录异常并返回异常。
<br>



### （五）	自定义局部过滤器
#### 1.	需求分析
&nbsp;  &nbsp;  &nbsp;  &nbsp;编写并配置一个自定义的局部过滤器，该过滤器可以通过配置文件中的参数名称获取请求的参数值。如将请求http://locahost:8084/user/8?name=xx中的参数name的值获取并且输出到控制台。注意，自定义过滤器的名称是由固定的后缀GatewayFilterFactory的，前面可以添加自定义的名称，如在前面加上MyParam，则自定义过滤器的名称为MyParamGatewayFilterFactory，在配置时只需要配置MyParam即可。
<br>



#### 2.	配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;在网关的配置中添加自定义的过滤器，如下所示：

```java
spring:
	cloud:
		gateway:
			routes:
				-id:user-service-route
				 uri: lb://user-service
				 predicates:
					-Path=/api/user/**
				 filters:
					-StripPrefix=1
					-MyParam=name
```
<br>



#### 3.	创建自定义过滤器
##### （1）创建配置类
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建自定义的过滤器MyParamGatewayFilterFactory，过滤器都要继承一个抽象类AbstractGatewayFilterFactory<>，需要传入一个泛型，我们在配置中配置了参数name，但是无法得知name的值的类型。因此需要创建一个配置类，动态地获取参数name的值，并将该类传到泛型的位置。


```java
public class MyParamGatewayFilterFactory extends AbstractGatewayFilterFactory<MyParamGatewayFilterFactory.Config>{
	public static class Config{
		private String param;
		public String getParam(){
			return param;
		}
		public void setParam(String param){
			this.param=param;
		}
} 
}
```
<br>



##### （2）添加构造函数

```java
public MyParamGatewayFilterFactory(){
	super(Config.class);
}
```
<br>



##### （3）添加shortcutFieldOrder函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;该函数的作用是将配置类中的参数添加到列表中，这样才能够从配置文件中读取数据。注意PARAM-NAME的值要和配置类中的参数名一致，都为param。

```java
static final String PARAM_NAME = “param”;
public List<String> shortcutFieldOrder(){
	return Arrays.asList(PARAM_NAME);
}
```
<br>



##### (4)重写apply方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;apply方法是过滤器的逻辑所在，如何过滤是该方法编写的内容。传入配置类作为参数，通过获取请求Request，判断请求参数中是否包含了我们需要的参数，如果有，就打印该参数。注意，此时在配置类中的config.param指的就是我们在网关的配置中填入的name参数。

```java
@Override
public GatewayFilter apply(Config config) {
    return (exchange, chain) -> {
        ServerHttpRequest request = exchange.getRequest();
        if(request.getQueryParams().containsKey(config.param)){
            request.getQueryParams().get(config.param).forEach(value-> System.out.printf("局部过滤器 ： %s = %s", config.param, value ));
        }
        return chain.filter(exchange);
    };
}
```
<br>



##### （5）	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;正常地通过网关进行访问，当我们在访问的url后面添加name参数时，如localhost:8084/7?name=xx控制台就会打印name参数的值xx了。
<br>



### （六）	自定义全局过滤器
#### 1.	需求分析
&nbsp;  &nbsp;  &nbsp;  &nbsp;定义一个全局过滤器，检查请求地址是否携带token参数，若token参数存在则放行；如果token参数不存在则设置返回的状态码为：未授权不再继续执行。
<br>



#### 2.	创建全局过滤器
&nbsp;  &nbsp;  &nbsp;  &nbsp;全局过滤器需要继承GlobalFilter类，如果想让过滤器的执行有序的话，还要继承Ordered类。重写filter方法与getOrder方法，filter方法中编写的是过滤器的过滤逻辑，判断获取请求的参数中是否有token参数，如果有则继续执行，如果没有则停止执行。getOrder方法是用于过滤器的排序，返回的值越小就越先执行。

```java
public class MyGlobalFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        System.out.println("去哪聚过滤器： ");
        String token = exchange.getRequest().getQueryParams().getFirst("token");
        if(StringUtils.isBlank(token)){
            //设置响应状态码为未授权
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        //值越小过滤器越先执行
        return 1;
    }
}
```
<br>



### （七）	Gateway的跨域问题
&nbsp;  &nbsp;  &nbsp;  &nbsp;跨域问题是在JS的请求中，如果访问的地址与当前服务器的域名，ip或者端口号不一致则成为跨域请求，若不能跨域则无法获取到请求地址的返回结果。因此，我们需要在网关中解决跨域问题，直接在网关中配置即可，配置有哪些服务是可以跨域请求的。配置如下：

```java
Spring:
cloud:
	gateway:
globalcors:
  cors-configurations:
    '[/**]':
      #表示允许访问的服务器地址
      allowedOrigins:
        - "http://doc.spring.io"
      allowedMethods:
        - GET
```



&nbsp;  &nbsp;  &nbsp;  &nbsp;allowedOrigins表示指定允许访问的服务器地址

&nbsp;  &nbsp;  &nbsp;  &nbsp;allowedMethods表示允许的方法

&nbsp;  &nbsp;  &nbsp;  &nbsp;‘[/**]’表示对所有访问到网关服务器的请求地址。

&nbsp;  &nbsp;  &nbsp;  &nbsp;因此上面的配置表示可以允许来自http://docs.spring.io的get请求方法获取服务数据。
<br>



### （八）	Gateway的高可用
&nbsp;  &nbsp;  &nbsp;  &nbsp;在服务内部之间访问时，可以通过启动多个Gateway服务，形成集群自动负载均衡。在外部访问Gateway时，这些请求无法通过Eureka进行负载均衡，需要使用其他的服务网关来对Gateway进行代理，如Nginx。
<br>



### （九）	Gateway与Feign的区别
&nbsp;  &nbsp;  &nbsp;  &nbsp;Gateway用于外部请求对微服务的调用，Feign则是将当前微服务的部分服务接口暴露出来，主要用于各个微服务之间的服务调用。

<br>


