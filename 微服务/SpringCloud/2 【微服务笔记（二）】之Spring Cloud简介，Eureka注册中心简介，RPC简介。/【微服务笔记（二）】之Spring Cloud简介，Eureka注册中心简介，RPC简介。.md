## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	服务调用方式
### （一）	RPC和HTTP
&nbsp;  &nbsp;  &nbsp;  &nbsp; 服务之间的远程调用方式有两种：RPC和HTTP。
<br>



#### 1.	RPC
&nbsp;  &nbsp;  &nbsp;  &nbsp; RPC全称为 Remote Produce Call远程过程调用，RPC是基于Socket，工作在会话层，自定义数据格式，速度快，效率高。代表产品为webservice,dubbo。
<br>



#### 2.	HTTP
&nbsp;  &nbsp;  &nbsp;  &nbsp; http是一种网络传输协议，基于TCP,工作在应用层，规定了数据传输的格式。客户端和服务器的通信基本都是HTTP协议，缺点是消息封装臃肿，优点是对于服务的提供和调用没有技术限定，不会限制使用什么语言，技术，HTTP只负责消息的传递。Rest风格，Spring Cloud套件就是通过HTTP协议实现的。
<br>



#### （二）	RPC与HTTP的区别

&nbsp;  &nbsp;  &nbsp;  &nbsp; RPC是根据语言的API来定义的，而不是根据网络的应用来定义的。它会限制使用语言，技术的灵活性。如果使用的JAVA语言，那么使用Dubbo比较合适。
如果不只是使用一种语言，技术，那么使用HTTP方式来实现服务间的调用更好。

<br>


#### （三）	Spring RestTemplate的使用
&nbsp;  &nbsp;  &nbsp;  &nbsp; 一般有三种http客户端工具类包可以方便地进行http服务调用：httpClient，okHttp，JDK原生URLConnection。Spring提供了RestTemplate的工具类对上述的三种http客户端工具进行了封装，可以直接在Spring项目中使用RestTempplate进行服务调用。

<br>



## 二．	Spring Cloud
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; Spring Cloud是Spring旗下的项目，完全支持Spring Boot的开发，用很少的配置就能够完成微服务框架的搭建。Spring Cloud能够将其他非常流行的技术整合到一起，如配置管理，服务发现，智能路由，负载均衡，熔断器，控制总线，集群状态等功能；协调分布式环境中各个系统，为各类服务提供模板性配置，
<br>



### （二）	组件构成
&nbsp;  &nbsp;  &nbsp;  &nbsp; Spring Cloud不是一个组件，而是许多组件的集合。主要的组件为：注册中心（Eureka），服务网关（Zuul, Gateway），负载均衡（Ribbon），服务调用（Feign），熔断器（Hystrix或Resilience4j）。当我们需要使用某些组件时，直接在项目中添加相关的启动器依赖即可。
<br>



### （三）	流程
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当请求通过网关进入后，负载均衡从服务注册中心中获取请求的地址列表，并基于负载均衡算法来选择一个地址来访问；如果服务之间有调用的需求，那么通过服务调用Feign进行调用；如果服务需要修改配置，那么从配置服务器中实时地更新配置。

<br>


### （四）	版本
&nbsp;  &nbsp;  &nbsp;  &nbsp; Spring Cloud 的版本不是普通的数字，而是使用伦敦地铁站的名字来命名的（脑残操作）。
<br>

## 三． Eureka注册中心
### （一）概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 上面的服务项目实例中，服务提供方对外提供服务，需要对外暴露自己的地址，而服务调用者需要记录服务提供者的地址，将来地址出现变动时，需要及时更新。当服务数量比较少时，这些不会造成困扰，一旦服务数量增多，管理上就非常麻烦。这就需要服务注册中心来统一管理。
<br>



### （二）Eureka注册中心
&nbsp;  &nbsp;  &nbsp;  &nbsp; Eureka负责管理 ，记录服务提供者的消息；服务调用者无需自己寻找服务，而是把自己的需求告诉Eureka，然后Eureka会把符合你需求的服务提供给你。同时，服务提供方与Eureka之间通过“心跳”机制来进行监控，当某个服务提供方出现问题时，Eureka会把它从服务列表中删除。通过服务注册中心实现了服务的自动注册，发现，状态监控。
<br>



### （三）	原理流程
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	服务提供者实例化服务，将服务注册到Eureka服务注册中心中。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	Eureka记录服务提供方的地址。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	服务消费方从Eureka中获取服务列表的地址。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 4.	基于负载均衡算法从地址列表中选择一个服务地址调用服务。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 5.	服务提供方会定期发送心跳给Eureka，告诉它这个服务时可用的。Eureka会定期检查那些没有定期发送心跳的服务，将它们在一定时间内剔除出服务地址列表、

<br>

