## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	SpringBoot整合Mybatis
### （一）	添加Mybatis的起步依赖

```xml
<!--mybatis起步依赖-->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.1.1</version>
</dependency>
```

### （二）	添加数据库驱动坐标

```xml
<!-- MySQL连接驱动 -->
	<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>
```

### （三）	添加数据库连接信息
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在application.properties配置文件中添加数据库连接信息

```xml
#DB Configuration:
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?
useUnicode=true&characterEncoding=utf8
spring.datasource.username=root
spring.datasource.password=root
```

### （四）	创建实体Bean

```java
public class User {
// 主键
private Long id;
// 用户名
private String username;
// 密码
private String password;
// 姓名
private String name;
//此处省略getter和setter方法 .. ..
}
```

### （五）	编写Mapper
&nbsp;  &nbsp;  &nbsp;  &nbsp; @Mapper注解标记该类是一个Mybatis的mapper接口，可以被SpringBoot自动扫描到Spring上下文中。

```java
@Mapper
public interface UserMapper {
public List<User> queryUserList();
}
```

### （六）配置Mapper映射文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在src\main\resources\mapper路径下添加UserMapper.xml配置文件：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.itheima.mapper.UserMapper">
	<select id="queryUserList" resultType="user">
select * from user
	</select>
</mapper>
```

### （七）	在application.properties中添加Mybatis的信息
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们需要修改SpringBoot的默认配置，让SpringBoot知道MyBatis扫描包以及映射文件位置：

```xml
#spring集成Mybatis环境
#pojo别名扫描包
mybatis.type-aliases-package=com.itheima.domain
#加载Mybatis映射文件
mybatis.mapper-locations=classpath:mapper/*Mapper.xml
```

### （八）	编写测试Controller

```java
@Controller
public class MapperController {
	@Autowired
	private UserMapper userMapper;
	@RequestMapping("/queryUser")
	@ResponseBody
	public List<User> queryUser(){
	List<User> users = userMapper.queryUserList();
	return users;
	}
}
```

## 二.	SpringBoot整合Junit
### （一）	添加Junit的起步依赖
&nbsp;  &nbsp;  &nbsp;  &nbsp; 一般SpringBoot中默认已经给我们设置了测试的起步依赖。

```xml
<!--测试的起步依赖-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
```

### （二）	编写测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = MySpringBootApplication.class)
public class MapperTest {
	@Autowired
	private UserMapper userMapper;
	@Test
	public void test() {
		List<User> users = userMapper.queryUserList();
		System.out.println(users);
	}
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 之前的`@RunWith`注解中的参数是SpringJunit4ClassRunner，这里的SpringRunner继承自SpringJunit4ClassRunner。
&nbsp;  &nbsp;  &nbsp;  &nbsp; `@SpringBootTest`注解中的参数指定的是SpringBoot引导类的字节码对象。意思是测试这个类的项目。

## 三.	SpringBoot整合Redis
### （一）添加Redis的起步依赖

```xml
<!-- 配置使用redis启动器 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### （二）	配置Redis的链接信息

```xml
#Redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

### （三）	注入RedisTemplate测试Redis

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = SpringbootJpaApplication.class)
public class RedisTest {
	@Autowired
	private UserRepository userRepository;
	@Autowired
	private RedisTemplate<String, String> redisTemplate;
	@Test
	public void test() throws JsonProcessingException {
		//从redis缓存中获得指定的数据
		String userListData = redisTemplate.boundValueOps("user.findAll").get();
		//如果redis中没有数据的话
		if(null==userListData){
			//查询数据库获得数据
			List<User> all = userRepository.findAll();
			//转换成json格式字符串
			ObjectMapper om = new ObjectMapper();
			userListData = om.writeValueAsString(all);
			//将数据存储到redis中，下次在查询直接从redis中获得数据，不用在查询数据库
			redisTemplate.boundValueOps("user.findAll").set(userListData);
			System.out.println("===============从数据库获得数据===============");
		}else{
			System.out.println("===============从redis缓存中获得数据===============");
		}
		System.out.println(userListData);
	}
}
```







