## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	SpringBoot
### （一）	SpringBoot的特点
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	约定优于配置，没有代码生成，也没有XML配置，同时也可以修改默认值来满足特定的需求。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	提供了一些大型项目中常见的非功能性特性，如嵌入式服务器，安全，指标，健康监测，外部配置等。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	SpringBoot不是对Spring功能上地增强，而是提供了一种快速使用Spring的方式。

### （二）	SpringBoot的核心功能
#### 1.	起步依赖
&nbsp;  &nbsp;  &nbsp;  &nbsp; 起步依赖的本质是一个Maven项目对象模型（Project Object Model，POM），定义了对其他库的传递依赖，这些东西加在一起支持某种功能，即打包一些坐标，提供默认的功能。

#### 2.	自动配置
&nbsp;  &nbsp;  &nbsp;  &nbsp; SpringBoot的自动配置是应用程序启动时考虑了多种因素，由SpringBoot自动决定Spring配置应该选择什么配置的过程。

### （三）	SpringBoot快速入门
#### 1.	创建Maven工程
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在IDEA中创建一个Maven工程。

#### 2.	添加SpringBoot的起步依赖
&nbsp;  &nbsp;  &nbsp;  &nbsp; SpringBoot的核心功能是起步依赖，我们需要在项目中继承SpringBoot的起步依赖spring-boot-starter-parent，

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.1.RELEASE</version>
</parent>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 同时，由于SpringBoot要集成SpringMVC进行Controller的开发，因此项目要导入Web的起步依赖，在之前的Spring项目中，我们不仅要导入Spring的坐标，还要导入SpringMVC的坐标以及其他坐标，现在，我们直接导入一个web的起步依赖就可以了。

```xml
<dependencies>
    <dependency>
        <!--Web功能的起步依赖-->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```


#### 3.	导入SpringBoot引导类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置完基本的信息后，我们需要添加一个SpringBoot的引导类，该类用于启动Spring，即运行SpringApplication，注意，SpringApplication的run函数中的参数是该引导类的字节码。同时要在该类上添加注解`@SpringBootApplication`标识这是一个SpringBoot的引导类。

```java
package com.springboot;

import org.springframework.boot.SpringApplication;

@org.springframework.boot.autoconfigure.SpringBootApplication
public class SpringBootApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootApplication.class);
    }
}

4.	编写Controller
在引导类的同级包或者子级包中创建一个Controller。Controller的写法以及注解与Spring中是相同的。

package com.springboot.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class QuickController {

    @RequestMapping("/quick")
    @ResponseBody
    public String quick(){
        return "hello springboot";
    }
}
```

### （四）	SpringBoot工程热部署
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在开发阶段，我们会反复地修改类，页面等资源，每次修改后都要重新启动修改才能够生效，这样非常麻烦，如何在不重启的情况下降修改生效呢？这就需要热部署来帮忙了。

 1.	首先在pom.xml中添加如部署的依赖

```xml
<!-- 热部署配置-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

 2.	接下来设置IDEA的自动编译
 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 选择File -> Settings -> Compiler 勾选 Build project automatically；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 然后按住 Shift + Ctrl + Alt + /，选择Registry，勾选 compiler.automake.allow.when.app.running。

3.	设置IDEA
	
	设置IDEA中的Spring Boot 设置Edit Configuration，将 On “Update” action 和 On frame deactivation 都改为 Update classes and resources。

### （五）	使用IDEA创建SpringBoot项目
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在新建项目处，选择Spring Initializer，初始化一个Spring项目，重点是选择各种SpringBoot需要的启动依赖，根据自己的需要进行选择，SpringBoot会自动将依赖添加到pom.xml文件中。

### （六）	SpringBoot原理分析
#### 1.	起步依赖原理分析
##### （1） 分析spring-boot-starter-parent
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们在pom.xml中继承了spring-boot-starter-parent，点进去可以发现它又继承了spring-boot-starter-dependencies，再点进去发现，spring-boot-starter-dependencies中都是依赖的坐标，依赖管理，插件管理。也就是说在SpringBoot工程中继承了spring-boot-starter-parent后已经具备版本锁定等配置了，因此起步依赖的作用就是进行依赖的传递。

#### （2）分析spring-boot-starter-web
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们在pom.xml中导入了web的依赖spring-boot-starter-web，只需要这一个依赖，我们就可以使用springmvc来开发，点进去看看发现spring-boot-starter-web就是将web开发要使用的spring-web，spring-webmvc等坐标进行了打包，同样是依赖传递的作用。

#### 2.	自动配置原理解析
&nbsp;  &nbsp;  &nbsp;  &nbsp; SpringBoot的自动配置是通过引导类的`@SpringBootApplication`注解完成的，点进去可以看到其中包括了很多注解，主要的是@SpringBootConfiguration，`@EnableAutoConfiguration`和`@ComponentScan`注解。

##### （1）@SpringBootConfiguration
&nbsp;  &nbsp;  &nbsp;  &nbsp; 这个注解表示这是一个配置类，我们可以在引导类中进行Spring的配置。

##### （2）@EnableAutoConfiguration
&nbsp;  &nbsp;  &nbsp;  &nbsp; 这个注解表示开启SpringBoot的自动配置功能。

##### （3）@ComponentScan
&nbsp;  &nbsp;  &nbsp;  &nbsp; 这个注解表示将会扫描引导类所在包下的所有类

### （七）	SpringBoot配置文件

#### 1.	SpringBoot配置文件作用
&nbsp;  &nbsp;  &nbsp;  &nbsp; SpringBoot是基于约定的，所以很多配置都有默认值，但是如果想使用自己的配置替换默认的配置的话，就可以使用application.properties或者application.yml(application.yaml)来进行配置。

&nbsp;  &nbsp;  &nbsp;  &nbsp; SpringBoot默认从Resources目录下加载application*.properties或者application.yml(application.yaml)文件。.properties的加载顺序在.yam文件之后，因此，.properties文件中的内容会覆盖.yml中的内容。

#### 2.	application*.yml配置文件
##### （1）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; YML文件格式是YAML（YAML Aint Markup Language）编写的文件格式，YAML是一种直观地能够被电脑识别的数据序列化格式，容易阅读也容易与脚本交互，可以被不同语言导入。扩展名为.yaml或者.yaml。

##### （2）	yml配置文件语法
###### a)	普通元数据
&nbsp;  &nbsp;  &nbsp;  &nbsp; yml用缩进表示等级，如普通的元数据是在冒号后面加上一个空格，如下所示：

```xml
key: value
```

###### b)	对象数据
&nbsp;  &nbsp;  &nbsp;  &nbsp; 对象拥有多个数据，同一层级的用相同的缩进表示，如下所示，person对象有多个属性：

```xml
person:
	name: haohao
	age: 31
	addr: beijing
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 或者可以写在同一行中，属性之间用逗号分隔开：

```xml
person:	{name: haohao,age: 31,addr: beijing}
```

###### c)	数组集合数据
&nbsp;  &nbsp;  &nbsp;  &nbsp; 对于数组，集合数据，就要使用 “-” 加上 空格来表示这是集合中的一个数据，如下所示：

```xml
city:
	- Beijing
	- Tianjin
	- shanghai
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 或者写在一行之中：

```xml
city: [Beijing,tianjin,shanhai]
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 若数组中有对象数据，那么直接将对象的属性一一写在数组的一个数据之中即可，如下所示：

student:
	- name: zhangsan
	  age: 18
	  score: 100
	- name: list
	  age: 21
	  score: 88

#### 3.	配置文件与配置类的属性映射方式
##### （1）使用注解@Value进行映射

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们在配置文件中配置了值以后，如何让Spring知道这些配置的作用呢？像Spring默认的配置键名Spring是认识的，但是对于那些我们自己创建的键名就无法识别了。因此，我们可以使用@Value注解将配置的键值映射到一个Spring管理的Bean的字段上。

如，配置文件application.yml中配置如下：

```xml
person:
name: zhangsan
age: 18
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们在类中将person对象的name属性和age属性一一映射：

```java
@Controller
public class QuickController{
		@Value(“${person.name}”)
		private String name;
		@Value(“${person.age}”)
		private Integer age;

		@RequestMapping(“/quick”)
		@ResponseBody
		public String quick(){
			return name + age;
		}
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这种方式映射地非常准确，但是也非常地繁琐，需要一个个地进行映射。

##### （2）使用注解@ConfigurationProperties映射
&nbsp;  &nbsp;  &nbsp;  &nbsp; 注解`@ConfigurrationProperties`能够将配置文件中的配置自动与实体进行映射，如在注解的参数中设置配置文件中键值作为前缀，只要类中的参数与配置文件中相同，就可以直接映射，且该参数必须提供get和set方法。

application.yml配置如下：

```xml
person:
name: zhangsan
age: 18
```

类中的代码如下所示：

```java
@Controller
@ConfigurationProperties(prefix = "person")
public class QuickStartController {
private String name;
private Integer age;
@RequestMapping("/quick")
@ResponseBody
public String quick(){
return "springboot 访问成功! name="+name+",age="+age;
}
public void setName(String name) {
this.name = name;
}
public void setAge(Integer age) {
this.age = age;
}
}
```



