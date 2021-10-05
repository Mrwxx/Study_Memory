## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Filter
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Filter过滤器，当访问服务器的资源时 ，过滤器可以将请求拦截下来，完成一些特殊的功能。
<br>


### （二）	作用
&nbsp;  &nbsp;  &nbsp;  &nbsp;过滤器一般完成通用的操作，如：登录验证，统一编码处理，敏感字符过滤
<br>



### （三）	过滤器的使用
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	定义一个类，实现Filter接口

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	重写接口中的方法

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	通过注解@WebFilter配置拦截路径，即 访问该路径的资源之前，都会执行该过滤器。或者通过web.xml配置拦截路径：
<br>



```xml
<filter>
	<filter-name>demo1</filter-name>
	<filter-class>cn.pro.filter.FilterDemo1</filter-class>
</filter>
<filter-mapping>
	<filter-name>demo1</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;4.	在doFilter方法中要对请求进行放行操作：filterChain.doFilter(servletRequest, ServletResponse)
<br>



### （四）	过滤器的执行流程
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	浏览器在请求服务器的资源时，要经过过滤器，过滤器对request对象请求消息进行增强处理。执行放行代码段之前的代码。

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	过滤器处理后，将request请求对象进行放行。执行放行代码段。

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	服务器将response发往浏览器的过程中，经过过滤器，过滤器对response对象进行增强处理。执行的是放行下面的代码段。

```java
System.out.println("filterDemo1执行了");
//放行
chain.doFilter(req, resp);

System.out.println("filterDemo2回来了");
```
<br>



### （五）	过滤器的生命周期方法
#### 1.	init方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;在服务器启动后，会创建Filter对象，然后调用init方法，只执行一次，用于加载资源。
<br>



#### 2.	doFilter方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;每一次请求资源被拦截时，会执行doFilter方法
<br>



#### 3.	destroy方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;在服务器关闭后，Filter对象被销毁，如果服务器是正常关闭，则会执行destroy方法，用于释放资源。
<br>



### （六）	过滤器的配置
#### 1．	过滤器的拦截路径配置
##### （1）	具体资源路径
&nbsp;  &nbsp;  &nbsp;  &nbsp;`/index.jsp`，只有当访问index.jsp资源时，过滤器才会被执行
<br>



##### （2）	拦截目录
&nbsp;  &nbsp;  &nbsp;  &nbsp;`/user/*` ，只有访问/user下的所有资源，过滤器才会被执行
<br>



##### （3）	后缀名拦截
&nbsp;  &nbsp;  &nbsp;  &nbsp;`*.jsp`，访问所有后缀名为jsp资源时，过滤器都会被执行
<br>



##### （4）	拦截所有资源
&nbsp;  &nbsp;  &nbsp;  &nbsp;/*	，访问所有资源时，过滤器都会被执行
<br>



#### 2.  拦截方式的配置
 &nbsp;  &nbsp;  &nbsp;  &nbsp;可以通过注解配置或者web.xml进行配置。
	
&nbsp;  &nbsp;  &nbsp;  &nbsp;注解配置中，设置dispatcherTypes属性，该属性可选值：
##### （1）	REQUEST
&nbsp;  &nbsp;  &nbsp;  &nbsp;默认值。表示浏览器直接请求资源

```java
@WebFilter(“/*”, dispatcherTypes=DispatcherType.REQUEST)
```
<br>



##### （2）	FORWARD
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示转发访问资源。
<br>



##### （3）	INCLUDE
&nbsp;  &nbsp;  &nbsp;  &nbsp;包含访问资源。
<br>



##### （4）	ERROR
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示错误跳转资源。
<br>



##### （5）	ASYNC
&nbsp;  &nbsp;  &nbsp;  &nbsp;异步访问资源。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果有多个限制条件，则用大括号括起来，如：

```java
dispatcherTypes = (DispatcherType.REQUEST, DispatcherType.ERROR)
```
<br>



### （七）	过滤器链
&nbsp;  &nbsp;  &nbsp;  &nbsp;当有多个过滤器链存在时，过滤器的执行顺序是怎样的？

如；有两个过滤器1，过滤器2，则执行顺序是：

&nbsp;  &nbsp;  &nbsp;  &nbsp;过滤器1 -> 过滤器2 -> 资源执行 -> 过滤器2  -> 过滤器1
<br>



### （八）	过滤器执行顺序问题
#### 1.	注解配置
&nbsp;  &nbsp;  &nbsp;  &nbsp;在注解中配置过滤器时，按照类名的字符串比较规则进行比较，值小的先执行
<br>



#### 2.	web.xml配置
&nbsp;  &nbsp;  &nbsp;  &nbsp;在web,xml中配置过滤器时，哪个过滤器的<filter-mapping>标签定义在上面，谁就先执行。
<br>




## 二．	过滤器实例
### （一）	登录验证
#### 1.	需求
&nbsp;  &nbsp;  &nbsp;  &nbsp;过滤器验证是否登录了，如果登录了，则直接放行；如果没有登录，则跳转到登录界面，输出提示。
<br>



#### 2.	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	由于我们访问资源时可能直接访问登陆相关的资源，那么此次请求就不应该被阻拦，应该直接放行。因此首先判断是否是登录相关的资源，如果是，则直接放行；如果不是，则判断是否登录。
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	要判断当前用户是否已经登录，就要通过Session中是否有User来判断，如果有，则放行；如果没有，则跳转到登录界面。

<br>



## 三．	代理模式
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;代理对象通过代理真实对象，以达到增强真实对象功能的目的。
<br>



### （二）	实现方式
#### 1.	静态代理
&nbsp;  &nbsp;  &nbsp;  &nbsp;有一个类文件描述代理模式
<br>



#### 2.	动态代理
&nbsp;  &nbsp;  &nbsp;  &nbsp;在内存中形成代理类，没有一个类文件代表代理对象。

实现步骤：

&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	代理对象和真实对象实现相同的接口

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	代理对象 = Proxy.newInstance();

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	使用代理对象调用方法

&nbsp;  &nbsp;  &nbsp;  &nbsp;（4）	增强方法
<br>



#### 3.	动态代理的使用
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	创建真实对象

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	创建动态代理对象增强真实对象。

&nbsp;  &nbsp;  &nbsp;  &nbsp;使用Proxy.newProxyInstance()方法创建动态代理对象，传入的参数为：
&nbsp;  &nbsp;  &nbsp;  &nbsp;类加载器：`真实对象.getClass().getClassLoader()`

&nbsp;  &nbsp;  &nbsp;  &nbsp;接口数组：`真实对象.getClass().getInterfaces()`

&nbsp;  &nbsp;  &nbsp;  &nbsp;处理器对象：`new InvocationHandler(){}`

&nbsp;  &nbsp;  &nbsp;  &nbsp;处理器为一个匿名对象，其中存放着代理增强的逻辑代码在invoke()方法中，代理对象调用的所有方法都会触发invoke()方法的执行：

```java
public Object invoke(Object proxy, Method method, Object[] args){}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;proxy:代理对象

&nbsp;  &nbsp;  &nbsp;  &nbsp;method:代理对象调用的方法，被封装为对象

&nbsp;  &nbsp;  &nbsp;  &nbsp;args:代理对象调用方法时，传递的真实参数

<br>



#### 4.	增强的方式
##### （1）	增强参数
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过args参数数组可以获取参数，因而可以修改参数。并重新通过真实方法将该参数传入调用invoke()方法。

```java
double money = (double)args[0];
money = money * 0.85;
Object obj = method.invoke(Lenovo,money);
return obj;
```
<br>



##### （2）	增强返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以通过修改返回值来增强。

```java
return obj + “_鼠标垫”;
```
<br>



##### （3）	增强方法体
&nbsp;  &nbsp;  &nbsp;  &nbsp;在方法体中添加操作，以增强真实对象。

<br>



## 四．	监听器
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;监听器，顾名思义用于监听事件的发生。
<br>



### （二）	监听机制
#### 1.	事件
&nbsp;  &nbsp;  &nbsp;  &nbsp;某个事件，如按下键盘
<br>



#### 2.	事件源
&nbsp;  &nbsp;  &nbsp;  &nbsp;事件发生的地方
	<br>



#### 3.	监听器
&nbsp;  &nbsp;  &nbsp;  &nbsp;用于监听事件发生的对象。
<br>



#### 4.	注册监听
&nbsp;  &nbsp;  &nbsp;  &nbsp;将事件，事件源，监听器绑定在一起，当事件源上发生某个事件后，执行监听器的代码。
<br>



### （三）	ServletContextListener监听器
&nbsp;  &nbsp;  &nbsp;  &nbsp;监听ServletContext对象的创建和销毁。
#### 1.	方法

```java
void contextDestroyed(ServletContextEvent sce)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;ServletContext对象被销毁前会调用该方法

```java
void contextInitialized(ServletContextEvent sce)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;ServletContext对象创建后会调用该方法
<br>



#### 2.	使用步骤
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	定义一个类，事项ServletContextListener接口

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	重写接口中的方法

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	配置文件

&nbsp;  &nbsp;  &nbsp;  &nbsp;通过web.xml或者注解都可以进行配置：

web.xml：

```xml
<listener>
<listener-class>全类名</listener-class>
</listener>
```
<br>



注解：

```java
@WebListener
```



