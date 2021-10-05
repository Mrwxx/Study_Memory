## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Dubbo入门案例
### （一）	案例概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 本案例要创建两个应用，一个作为服务的提供方，一个作为服务的消费方，通过Dubbo来实现服务消费方远程调用服务提供方的方法。
<br>



### （二）	服务提供方开发
#### 1.	创建Maven工程dubboDemo_Provider
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建一个新的Maven工程，由于这是一个web项目，因此我们可以使用webapp的骨架进行搭建Maven项目。
<br>



#### 2.	配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在pom.xml中导入相关的坐标，如spring相关的坐标，dubbo相关的坐标，以及数据库相关的坐标。

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jms</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>${spring.version}</version>
  </dependency>
  <!-- dubbo相关 -->
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.6.0</version>
  </dependency>
  <dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.7</version>
  </dependency>
  <dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
  </dependency>
  <dependency>
    <groupId>javassist</groupId>
    <artifactId>javassist</artifactId>
    <version>3.12.1.GA</version>
  </dependency>
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.47</version>
  </dependency>
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.11</version>
  </dependency>
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.6</version>
  </dependency>
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.2</version>
  </dependency>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```
<br>



#### 3.	配置web.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 这是一个web项目，因此我们需要配置web.xml文件，添加一个监听器，当web项目启动时，自动读取配置文件application.xml，配置文件的地址通过`<context-param>`标签给出。

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:application*.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
</web-app>
```
<br>



#### 4.	创建服务接口
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建一个HelloService接口，声明一个方法，同时创建它的实现类HelloServiceImpl，实现接口的方法，并且在该实现类的上面用@Service注解标注，注意这里使用的@Service注解不是spring框架中的注解，而是Dubbo提供的@Service注解，因为要发布服务必须使用Dubbo提供的@Service注解。

```xml
package com.dubbo.service.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.dubbo.service.HelloService;

@Service //发布服务使用Dubbo提供的Service注解
public class HelloServiceImpl implements HelloService {

    @Override
    public String sayHello(String name) {
        return "hello " + name;
    }
}
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 5.	在src/main/resources下创建applicationContext-service.xml
这是spring服务的配置文件，我们可以在其中配置dubbo的各种设置。

&nbsp;  &nbsp;  &nbsp;  &nbsp; dubbo的应用名称，如现在需要配置的是服务提供方的应用名称dubboDemo_Provider：

```xml
<dubbo:application name=”dubboDemo_Provider”/>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注册中心的地址，地址中的zookeeper为固定写法

```xml
<dubbo:registry address=”zookeeper://192.168.134.129:2181”/>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注册dubbo的协议和端口号，端口号默认是20880

```xml
<dubbo:protocol name=”dubbo” port=”20880”></dubbo:protocol>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 扫描指定的包，那些加入了@Service注解的类会被发布为服务

```xml
<dubbo:annotation package=”com.dubbo.service.impl”>
```
<br>



### （三）	服务消费方开发
#### 1.	创建Maven项目
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同理，依然是依据webapp骨架创建一个Maven项目。
<br>



#### 2.	配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置情况与服务提供方相同，不过注意在Tomcat插件的端口处要和服务提供方不同。
<br>



#### 3.	配置web.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 服务消费方的web.xml配置和服务提供方是不同的，消费方是要调用服务提供方的服务的，因此配置的是SpringMVC中的前端控制器。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这里以servlet作为前端控制器，当有以do为后缀的请求时，servlet将请求转发到对应的Controller上，并且指定服务启动时加载的配置文件。

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:applicationContext-web.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>
</web-app>
```
<br>



#### 4.	将服务提供方的HelloService接口复制到当前的服务消费方中
&nbsp;  &nbsp;  &nbsp;  &nbsp; 直接复制服务提供方的接口。
<br>



#### 5.	编写Controller
&nbsp;  &nbsp;  &nbsp;  &nbsp; 服务消费方使用过Controller来调用服务提供方的服务方法的，因此创建一个Contoller的类，远程调用服务的方法sayHello。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这里，我们按照往常一样在类上设置@Controller注解和@RequestMapping注解。并且注入要调用的服务接口 HelloService，但是这里不能使用@Autowired自动注入，因为远程调用服务提供方的服务，该服务不在内存中，自然不能自动注入。需要使用Dubbo提供的@Reference注解来引用注册中心中的服务。

```xml
package com.dubbo.controller;

import com.alibaba.dubbo.config.annotation.Reference;
import com.dubbo.service.HelloService;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping(".demo")
public class HelloController {

    @Reference
    private HelloService  helloService;

    @RequestMapping("/hello")
    @ResponseBody
    public String getName(String name){
        //远程调用
        String result = helloService.sayHello(name);
        System.out.println(result);
        return result;
    }
}
```
<br>



#### 6.	配置applicationContext-web.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同理，需要配置spring web层的配置文件，同样是配置应用名称，链接注册中心的地址，这个地址是我们在linux服务器中启动的，以及扫描包。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/mvc
         http://www.springframework.org/schema/mvc/spring-mvc.xsd
         http://code.alibabatech.com/schema/dubbo
         http://code.alibabatech.com/schema/dubbo/dubbo.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd">
    <dubbo:application name="dubboDemo_Consumer"></dubbo:application>
    <dubbo:registry address="zookeeper://192.168.211.128:2181"></dubbo:registry>
    <dubbo:annotation package="com.dubbo.controller"></dubbo:annotation>
    <dubbo:consumer check="false"></dubbo:consumer>
</beans>
```
<br>



### （四）	测试
&nbsp;  &nbsp;  &nbsp;  &nbsp; 注册中心zookeeper服务启动后，还要启动服务提供方的项目，服务消费方的项目，如果项目的启动都没有问题的话，访问服务消费方的地址，并且访问Controller的方法，并且添加参数name:

```xml
http://localhost:8084/demo/hello.do?name=wwxx
```

得到页面的显示结果为：

```xml
hello wwxx
```

测试成功！！！

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可能会出现Zookeeper连接不上的问题，首先要检查Linux中安装的Zookeeper的版本与项目中导入的版本是否一致；然后再检查Ubuntu 中是否关闭了防火墙，如果没有，需要将防火墙关闭！！！
<br>



### （五）	思考
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	之前直接将服务提供方的接口复制到服务消费方中，这是一个不好的做法。更好的解决方法是单独创建一个Maven工程，将该接口创建在这个Maven工程中。需要依赖此接口的工程只需要在自己的工程pom.xml中引入maven坐标即可。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	服务消费方只是引用了接口，并没有提供实现类，Dubbo是如何远程调用的？

Dubbo底层是基于代理技术为接口创建代理对象，远程调用是通过此代理对象完成的。可以通过开发工具的debug功能查看此代理对象的内部结构，另外，Dubbo实现网络传输底层是基于Netty框架实现的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;  3.	如何防止Zookeeper单点故障？

&nbsp;  &nbsp;  &nbsp;  &nbsp; Zookeeper是支持集群模式的，可以配置Zookeeper集群来达到Zookeeper服务的高可用性，防止出现单点故障。

<br>
