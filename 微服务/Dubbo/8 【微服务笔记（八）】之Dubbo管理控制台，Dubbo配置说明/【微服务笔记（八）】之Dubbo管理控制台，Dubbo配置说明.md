
## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Dubbo管理控制台
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在开发中，我们需要知道Zookeeper注册中心都注册了哪些服务，有哪些消费者来消费这些服务，我们可以通过部署一个管理中心来实现。这个管理中心是一个web应用，部署在Tomcat服务器上。
<br>



### （二）	安装步骤

&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	将下载好的 dubbo-admin-2.6.0.war文件复制到本地Tomcat安装文件夹的webapps目录下。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	在Tomcat的安装目录下的bin目录中，点击startup.bat批处理文件，启动Tomcat，这个Dubbo管理控制台的war包会自动被解压成文件夹。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	在同名文件夹中，修改WEB-INF目录下的dubbo.properties文件，将Zookeeper的ip地址改为当前使用的Zookeeper的ip地址和端口号。

```xml
dubbo.registry.address=zookeeper://192.168.211.128:2181
dubbo.admin.password=root
dubbo.admin.guest.password=guest
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，这里有两个用户，一个是root用户，一个是guest用户，root用户的密码为root，guest用户的密码为guest。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 4.	重新启动Tomcat
<br>




## 二．	Dubbo相关配置说明
### （一）	包扫描
&nbsp;  &nbsp;  &nbsp;  &nbsp; 服务提供者和服务消费者都需要配置包扫描，作用是扫描指定包（包括子包）下的类。

```xml
<dubbo:annotation package=”com.dubbo.service”>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果不适用包扫描，也可以使用配置的方式来发布服务。如

服务提供方通过注册服务：

```xml
<bean id=”helloService” class=”com.dubbo.service.impl.HelloServiceImpl”/>
<dubbo:service interface=”com.dubbo.service.HelloService” ref=”helloService”>
```

服务消费方，通过配置引用服务：

```xml
<dubbo:reference id=”helloService” interface=”com.dubbo.service.helloService”/>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这种通过配置来发布和引用服务的做法，一个配置项（dubbo:service，dubbo:reference）只能发布或者引用一个服务，如果有多个服务，这种方式就非常地繁琐，这里推荐使用地是包扫描方式。
<br>



### （二）	协议
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在服务提供者的配置中，可以指定使用的协议名称和端口号：

```xml
<dubbo:protocal name=”dubbo” port=”20880”/>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; Dubbo支持的协议有：dubbo, rmi, hessian, http, webservice, rest, redis等，官方推荐使用的是dubbo协议。

&nbsp;  &nbsp;  &nbsp;  &nbsp; dubbo协议采用单一长连接和NIO异步通信，是和于小数据量大并发的服务调用，以及服务者机器数远大于服务提供者机器数的情况，不适合传送大数据量的服务，比如传文件，视频。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们也可以在同一个工程中配置多个协议，在不同服务中可以使用不同的协议，直接通过注解设置或者在配置文件中设置：

注解设置多协议：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在服务提供方的服务注解@Service中设置protocol属性即可。

		

```xml
配置文件设置多协议：
		<dubbo:protocol name=”dubbo” port=”20880”/>
		<dubbo:protocol name=”rmi” port=”1099”/>
		<dubbo:service interface=”com.dubbo.service.HelloService” ref=”helloService” protocol=”dubbo”/>
		<dubbo:service interface=”com.dubbo.service.DemoService” ref=”demoService” protocol=”rmi”/>
```
<br>



### （三）	启动时检查
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们之前是先启动服务提供方的服务器，再启动服务消费方的服务器，这样服务消费方能够检测到服务提供方注册的服务。若我们将两者的启动顺序调换，就会出错，这是服务消费方的启动时检查的结果，它会检查依赖的服务时是否可用，不可用时会抛出异常，阻止Spring初始化完成。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 为了尽快上线，及早地发现问题，我们在开发阶段可以关闭启动时检查，在生产环境下改为true。

```xml
<dubbo:consumer check=”false”/>
```
<br>



### （四）	负载均衡
&nbsp;  &nbsp;  &nbsp;  &nbsp; 负载均衡是将请求分摊到多个操作单元上进行执行，从而共同完成工作任务。在集群负载均衡时，Dubbo提供了多种均衡策略，包括危机，轮训，最少活跃调用数，一致性Hash，默认为随机调用。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置负载均衡策略，既可以在服务提供者一方进行配置，也可以在服务消费者一方进行配置，

如：
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在服务消费者一方配置负载均衡

```java
@Controller
@RequestMapping(“/demo”)
public class  HelloController{
	@Reference(check=false,loadbalance=”random”)
	private HelloService helloService;

	@RequestMapping(“/hello”)
	@ResponseBody
	public String getName(String name){
		String result = helloService.sayHello(name);
		System.out.println(result);
		return result;
	}
}
```

在服务提供者一方配置负载均衡

```java
@Service(loadbalance=”random”)
public class HelloServiceImpl implements HelloService{
	public String sayHello(String name){
		return “hello” + name;
	}
}
```

<br>



## 三．	解决Dubbo无法发布被事务代理的Service问题
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果在服务提供者类上添加 @Transactional事务控制注解后，服务就发布失败了，原因是事务控制的底层原理是为服务提供者创建代理对象，而默认情况下Spring是基于JDK 动态代理方式创建代理对象，而此代理对象的全类名为 com.sun.proxy.$Proxy42，导致Dubbo在发布服务前进行包匹配时无法完成，因为我们在发布服务时扫描的包围com.dubbo.service，所以真正发布服务的代码未执行。

解决方法：
&nbsp;  &nbsp;  &nbsp;  &nbsp; （1）	修改applicationContext-service.xml配置文件

&nbsp;  &nbsp;  &nbsp;  &nbsp; 开启事务控制注解支持时指定proxy-target-class属性，值为true，其作用是使用cglib代理方式为Service类创建代理对象。

```xml
<tx::annotation-driven transaction-manager=”transactionManager” proxy-target-class=”true”/>
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; （2）	修改服务提供方的实现类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在@Service注解中加入interfaceClass属性，值为HelloService.class，作用是指定服务的接口类型，这样，服务提供方就会知道创建代理对象的接口为HelloService了，而不是SpringProxy，该服务能够发布成功。

服务消费方：

```java
@Service(interfaceClass=helloService.class)
@Transactional
public class HelloSericeImpl  implements implements HelloService{
		public String sayHello(String name){
				return “hello” + name;
			{
}
```




