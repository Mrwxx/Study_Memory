## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	Spring IoC
&nbsp;  &nbsp;  &nbsp;  &nbsp;Ioc容器是Spring的核心，可以说Spring是一种基于IOC容器编程的框架。它通过描述来生成或者获取对象，之前是通过XML来描述，这里使用全注解的方式来描述。一个系统可以生成各种对象，这些对象都需要管理，并且它们之间存在着依赖关系，通过描述就能够管理它们之间的关系。
<br>


## 二.	IOC容器
&nbsp;  &nbsp;  &nbsp;  &nbsp;在Spring中我们把每一个需要管理的对象成为Spring Bean，而Spring 管理这些Bean的容器，称为Spring IoC容器，IOC容器有两个功能，一个是负责发布和获取Bean，一个是负责管理Bean之间的依赖管理。
<br>



## 三.	BeanFactory接口
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring中所有的IoC容器都要实现接口BeanFactory，它是一个顶级容器接口。翻看源码，可以看到接口中的方法有如下的方法：
#### 1.	getBean方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;它的意义是从IOC容器中获取Bean，可以按Bean类型，名称来获取。
<br>



#### 2.	containsBean方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;判断是否包含某个Bean。
<br>



#### 3.	isSingleton方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;判断Bean是否在Spring IoC中为单例，默认情况下，Bean都是以单例的形式存在的，即调用getBean方法返回的都是同一个Bean对象。
<br>



#### 4.	isPrototype方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;判断Bean是否在Spring IoC是原型，即不是单例。如果为true，则Spring IoC容器会创建一个新的Bean返回给调用者。
<br>



## 四.	ApplicationContext接口
&nbsp;  &nbsp;  &nbsp;  &nbsp;为了扩展BeanFactory接口，还设计了一个更加高级的接口ApplicationContext，它是BeanFactory接口的子接口之一。在BeanFactory的基础上，扩展了消息国际化接口（MessageSource），环境可配置接口（EnvironmentCapable），应用事件发布接口（ApplicationEventPublisher）和资源模式解析接口（ResourcePatternResolver）。在开发中我们使用过的大部分Spring IoC容器都是ApplicationContext接口的实现类。
<br>



## 五.	AnnotationConfigApplicationContext
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring Boot中使用全注解来开发，因此使用一个基于注解的IOC容器，它就是AnnotaionConfigApplicationContext。下面用个小Demo说明：

#### 1.	POJO
&nbsp;  &nbsp;  &nbsp;  &nbsp;定义一个POJO，User类。

```java
package com.springboot.chap3.pojo;

public class User {
    private String id;
    private String userName;
    private String note;

    public User() {
    }

    public User(String id, String userName, String note) {
        this.id = id;
        this.userName = userName;
        this.note = note;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getNote() {
        return note;
    }

    public void setNote(String note) {
        this.note = note;
    }
}
```
<br>



#### 2.	定义一个Java配置类
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们的目的是要将POJO类生成Bean丢到Spring IoC容器中，那么就要创建一个配置类，在类中创建一个Bean配置到Spring Ioc容器中。@Bean注解表示这是一个Bean，@Configuration注解表示这是一个Java配置文件，Spring容器会根据它来生成IOC容器去装配Bean。

```java
package com.springboot.chap3.config;

import com.springboot.chap3.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean(name = "user")
    public User initUser(){
        User user = new User();
        user.setId("aaa");
        user.setUserName("username1");
        user.setNote("note1");
        return user;
    }
}
```
<br>



#### 3.	测试类构建IOC容器
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用AnnotationConfigApplicationContext来新建一个IOC容器，将配置类传进去，让IOC容器的构造方法读取配置类，然后将配置里面的Bean装配到IOC容器中。接下来就可以调用IOC容器中的方法了。

```java
package com.springboot.chap3.test;

import com.springboot.chap3.config.AppConfig;
import com.springboot.chap3.pojo.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import java.util.logging.Logger;

public class IocTest {

    private static Logger log = Logger.getLogger(String.valueOf(IocTest.class));

    public static void main(String[] args) {
        ApplicationContext atx = new AnnotationConfigApplicationContext(AppConfig.class);
        User bean = atx.getBean(User.class);
        log.info(bean.getId());
    }
}
```
<br>

