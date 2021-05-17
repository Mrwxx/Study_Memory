## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	扫描Bean
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;之前，我们通过构造一个IOC容器来获取Bean，每个Bean对象都要添加@Bean注解，当Bean对象比较多的时候非常麻烦。这里我们使用@ComponentScan和@Component注解进行扫描装配Bean。
<br>



#### 2.	@Component注解
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个注解用于哪个类就表明哪个类被扫描进入Spring IoC容器中，注解中的参数是Bean对象的名称，默认是当前类名的第一个字母小写，其他不变。注解@Value指定具体的值，使得Spring IoC给予对应的属性注入对应的值。

```java
@Component("user")
public class User {
    @Value("aaa")
    private String id;
    @Value("username")
    private String userName;
    @Value("note")
    private String note;
}
```

<br>



#### 3.	@ComponentScan注解
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个注解表明采用何种策略来扫描装配Bean对象。可以通过配置项basePackages自定义需要扫描的类，以及设置过滤器，过滤掉不需要的类。配置项includeFilters定义满足过滤器条件的Bean才去扫描，excludeFilters则是排除过滤器条件的Bean，都需要注解@Filter来定义，使用的一般是正则表达式。如：

```java
@ComponentScan(“com.springboot.chapter3.*”)或
@ComponentScan(basePackages=”com.springboot.chapter3.pojo”)
@ComponentScan(basePackageClasses={User.class})
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;添加过滤器的@ComponentScan，这样UserService就不会被扫描到：

```java
@Component(basePackages=”com.springboot.chapter3.*”, excludeFilter = {@Filter(classes={UserService.class})})
```
<br>



## 二.	自定义第三方Bean
&nbsp;  &nbsp;  &nbsp;  &nbsp;Java应用需要引入第三方的包，且需要将第三方的类对象注入Spring IoC中，这时还是要使用@Bean注解，在需要注入的类上添加@Bean注解，这样就能够扫描到了。
<br>



## 三.	依赖注入
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring IoC还需要负责Bean之间的依赖关系，这里通过注解来配置。
<br>



#### 1.	@Autowired
&nbsp;  &nbsp;  &nbsp;  &nbsp;当需要注入一个Bean时，使用@Autowired注解来表示注入，它注入的机制基本的一条是根据类型，但是当一个Bean有多个时，就会出现异常。这时它就会首先根据类型找到Bean，再根据属性名称和Bean的名称进行匹配，如果匹配不成功，则抛出异常。同时，这个注解默认是必须找到对应的Bean对象的，如果不确定它标注的属性一定会存在且允许这个被标注的属性为null时，我们可以设置@Autowired属性required为false，那么该注解就可以不用找到对应的Bean对象了。
<br>



#### 2.	@Pirmary
&nbsp;  &nbsp;  &nbsp;  &nbsp;当出现注入歧义时，可以使用@Primary注解来修改注入的优先级。在优先级更高的注入类中添加该注解，那么就会首先找到该类来进行注入。
<br>



#### 3.	@Qualifier
&nbsp;  &nbsp;  &nbsp;  &nbsp;该注解配合@Autowired来使用，能够更加精确地注入，该注解的配置项value需要一个字符串，即需要注入的Bean对象名称，这样就能消除歧义性了。如：

```java
@Autowired
@Qualifier(“dog”)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注解同样可以在参数中使用，如下所示：

```java
public BussinessPerson(@Autowired @Qualifier(“dog”) Animal animal){
	this.animal = animal;
}
```
<br>



## 四.	生命周期
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring IoC容器如何初始化和销毁Bean，这是Bean的生命周期。它大致分为Bean定义，Bean初始化，Bean的生存期和Bean的销毁四个部分。

<br>


#### 1．	Bean定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;Spring 通过扫描包扫描到带有@Component的类后，开始解析，并将定义的信息保存起来，然后就会把Bean定义发布到Spring Ioc容器中，此时IOC容器中只有Bean的定义，没有Bean的实例生成，更没有依赖注入。
<br>



#### 2.	Bean初始化
&nbsp;  &nbsp;  &nbsp;  &nbsp;默认情况下，Spring在定义后，会完成Bean的实例化和依赖注入，这样从IOC容器中就可以得到一个依赖注入完成的Bean。@ComponentScan中有一个配置项lazyInit，默认是false，表示默认不进行延迟初始化，即默认Spring会完成Bean的实例化和依赖注入。
<br>



#### 3.	自定义Bean初始化与销毁

&nbsp;  &nbsp;  &nbsp;  &nbsp;在Spring完成依赖注入后，还提供了一系列的接口和配置来完成Bean初始化的过程。

##### （1）	接口BeanNameAware
&nbsp;  &nbsp;  &nbsp;  &nbsp;该接口中的setBeanName方法用于设置Bean的名称。
<br>



##### （2）	接口BeanFactoryAware
&nbsp;  &nbsp;  &nbsp;  &nbsp;该接口中的setBeanFactory方法用于设置BeanFactory。
<br>



##### （3）	接口ApplicationContextAware
&nbsp;  &nbsp;  &nbsp;  &nbsp;该接口需要一个实现了ApplicationContext接口的IOC容器，这样该接口的setApplicationContext方法才会被调用。
<br>



##### （4）	接口BeanPostProcessor
&nbsp;  &nbsp;  &nbsp;  &nbsp;该接口为Bean后置处理器，将对所有的Bean有效，它有两个方法：预初始化方法postProcessBeforeInitialization，后初始化方法postProcessAfterInitialization。
<br>



##### （5）	注解@PostConstruct
&nbsp;  &nbsp;  &nbsp;  &nbsp;该注解用于表示自定义的初始化方法。
<br>



##### （6）	接口InitializingBean
&nbsp;  &nbsp;  &nbsp;  &nbsp;该接口中有一个afterPropertiesSet方法，用于设置属性。
<br>


	
##### （7）	注解@PreDestory
&nbsp;  &nbsp;  &nbsp;  &nbsp;该注解用于自定义一个销毁方法。
<br>



##### （8）	接口DIsposableBean
&nbsp;  &nbsp;  &nbsp;  &nbsp;该接口的destory方法用于销毁Bean。

<br>
