## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 	使用配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;在Spring Boot中，我们首先要在Maven配置文件中加载依赖，如下所示，这样Spring 将创建读取配置文件的上下文：

```java
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-configuration-processor</artifactId>
		<optional>true</optional>
</dependency>
```
<br>



#### 1.	使用默认application.properties文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用默认的application.properties文件时，它会自动被Spring读取到上下文中，可以直接引用，无需其他操作。一般引用的方式是Spring表达式，通过@Value注解，其中用${..}这样的占位符来读取配置在配置文件中的值。如下所示：

```java
@Value(“${database.driverName}”)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;表示 读取配置文件中的 database.driverName 字段。

&nbsp;  &nbsp;  &nbsp;  &nbsp;也可以使用注解@ConfigurationProperties，该注解可以配置前缀属性，该前缀可以和该类中的属性名称组合起来，组成一个全限定名去配置文件中查找，这样就不需要为每一个属性添加注解@Value了。如下所示：

```java
@ConfigurationProperties(“database”)
public class DataBaseProperties{
	private String driverName = null;
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring就会以 “database.driverName”的全限定名去查找配置文件。
<br>



#### 2.	使用其他的配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;当需要将默认的配置文件拆分时，就需要使用注解@PropertyScan来引用需要的文件了，将它加载到Spring的上下文中。注解中的value属性为多个配置文件的名称，classpath表示在这个类的路径下去查找配置文件， ignoreResouceNotFound表示是否忽略配置文件找不到的情况。如下所示：

```java
@SpringBootApplication
@ComponentScan(basePackages = {"com.pojo"})
@PropertySource(value={"classpath:jdbc.properties"}, ignoreResourceNotFound = true)
public class spb {
    public static void main(String[] args) {
        SpringApplication.run(spb.class);
    }
}
```
<br>




## 二. 条件装配Bean
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;有时候因为一些客观因素导致一些Bean无法初始化，再这样的情况下，如果IOC容器继续装配Bean就会抛出异常。这里我们可以通过判断上下文中Bean所需要的参数是否齐全来决定是否装配Bean。
<br>



#### 2.	注解@Conditional
&nbsp;  &nbsp;  &nbsp;  &nbsp;该注解需要配合另一个接口Condition一起使用，在需要判断的类上使用注解@Conditional()，括号内的参数为实现了接口Condition的实现类。如下所示，DatabaseConditional类实现了接口Condition。
<br>



#### 3.	接口Condition
&nbsp;  &nbsp;  &nbsp;  &nbsp;接口Condition要求实现mathes方法，该方法用于判断上下文中所需参数是否齐全，是否可以装配Bean。如下所示，context参数为上下文，从上下文中取出环境配置，查看环境配置中是否包含所需要的参数，返回true表示能够装配Bean：

```java
public class DatabaseConditional implements Condition{
	@Override
	public Boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata){
		Environment env = context.getEnvironment();
		return env.containsProperty(“database.driverName”)
			&& env.containsProperty(“database.url”)
			&& env.containsProperty(“database.username”)
			&& env.containsProperty(“database.password”)
	}
}
```
<br>



## 三. Bean的作用域
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;在一般的容器中，Bean有单例（Singleton）和原型（Prototype）两种作用域；而在Web容器中，则有页面（Page），请求（Request），会话（Session）和应用（Application）四种作用域。
<br>



#### 2.	注解@Scope
&nbsp;  &nbsp;  &nbsp;  &nbsp;该注解用于定义类的作用域，单例或是原型，如下所示：
单例：

```java
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
```

原型：

```java
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;在Spring MVC环境中，还可以使用WebApplicationContext来定义其他的作用域，如下所示：

```java
@Scope(WebApplicationContext.SCOPE_REQUEST)
```
<br>



## 四. 引入XML配置
&nbsp;  &nbsp;  &nbsp;  &nbsp;在Spring Boot中也可以引入XML的配置，使用注解@ImportResource，其中的value属性为需要引入的XML配置文件的名称，如下所示：

```java
@ImportResource(value={“classpath:spring-other.xml”})
```

<br>


## 五. 使用Spring EL
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring提供了表达式语言Spring EL，能够读取配置文件属性，调用方法，进行简单运算。

#### 1.	${…}
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个符号表示占位符，它会读取上下文的属性值装配到属性中。如下所示：

```java
@Value(“${database.driverName}”)
String driver;
```
<br>



#### 2.	#{…}
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个符号表示启用Spring表达式，它将具有运算的功能，T(…)表示引入类，类需要写全限定名，引入类后就可以直接调用方法了，如下所示：

```java
@Value(“#{System}.currentTimeMillies())”)
private Long initTime = null;
```
<br>



#### 3.	获取其他Bean的属性
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以直接在Spring EL中获取其他Bean的属性用于赋值，如下所示：

```java
@Value(“#{beanName.str}”)
private String BeanProp = null;
```






