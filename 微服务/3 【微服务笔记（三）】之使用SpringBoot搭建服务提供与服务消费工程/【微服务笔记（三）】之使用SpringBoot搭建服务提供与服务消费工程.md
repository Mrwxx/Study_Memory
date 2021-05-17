## 本文章由公号【开发小鸽】发布！欢迎关注！！！

<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．服务提供方实例
### （一）.	创建项目工程
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建微服务父工程SpringCloudParentDemo，用户服务提供工程SpringCloudProducer，服务消费工程SpringCloudConsumer。后面两个工程继承父工程，父工程添加Spring Boot父坐标和管理其他组件的依赖；用户服务工程用于整合Mybatis查询数据库中用户数据，提供查询用户数据服务；服务消费工程用于调用查询用户服务数据并输出到浏览器。
<br>



### （二）	配置父工程pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.SpringCloud</groupId>
    <artifactId>com.SpringCloud.ParentDemo</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>SpringCloudConsumer</module>
        <module>SpringCloudProducer</module>
    </modules>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.5.RELEASE</version>
        <relativePath/>
    </parent>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
        <mapper.starter.version>2.1.5</mapper.starter.version>
        <mysql.version>5.1.46</mysql.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!-- springCloud -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- 通用Mapper启动器 -->
            <dependency>
                <groupId>tk.mybatis</groupId>
                <artifactId>mapper-spring-boot-starter</artifactId>
                <version>${mapper.starter.version}</version>
            </dependency>
            <!-- mysql驱动 -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-config</artifactId>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.0</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;   modules标签内表示的是该父工程中子工程的名称；

  &nbsp;  &nbsp;  &nbsp;  &nbsp; parent标签内继承了SpringBoot启动器依赖；
  
  &nbsp;  &nbsp;  &nbsp;  &nbsp; properties标签内表示的是其他配置信息，如java版本，SpringCloud版本，mybatis版本，mysql驱动版本，后面可以直接引用这些版本信息。
  
  &nbsp;  &nbsp;  &nbsp;  &nbsp; dependencyManagement标签内表示的是依赖的坐标管理，只是限定我们需要依赖的坐标，但是并没有真正地导入这些坐标。真正地导入坐标需要在每个子工程中自行导入。
  
  &nbsp;  &nbsp;  &nbsp;  &nbsp; 由于spring-cloud-dependencies依赖的scope是import，因此只要包括了该依赖的项目都能够继承该工程中的依赖，因此父工程项目不仅继承了spring-boot-parent，还继承了spring-cloud-dependencies，不需要自己再定义SpringCloud组件的版本了。

<br>


### （三）配置服务提供子工程的pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于父工程中已经配置了依赖的坐标管理，因此，我们直接在子工程导入已经管理的坐标即可，如Mapper启动器，mysql驱动，不需要定义版本。同时还有web启动器。

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- 通用Mapper启动器 -->
    <dependency>
        <groupId>tk.mybatis</groupId>
        <artifactId>mapper-spring-boot-starter</artifactId>
    </dependency>
    <!-- mysql驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
</dependencies>
```
<br>



### （四）创建引导类和SpringBoot配置文件appliaction.yml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建引导类入口:

```java
@SpringBootApplication
public class userApplication {
    public static void main(String[] args) {
        SpringApplication.run(userApplication.class, args);
    }
}
```

<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置application.yml，其中包括服务器的端口号，使用数据源的驱动，地址，用户名，密码，同时还有设置mybatis别名包的扫描，这个包和包中的实体类需要自行创建。


```yml
server:
  port: 9091
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/springcloud
    username:root
    password:

mybatis:
  type-aliases-package: com.user.pojo
```


<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建实体类User:

```java
package com.user.pojo;

import lombok.Data;
import tk.mybatis.mapper.annotation.KeySql;

import javax.persistence.Id;
import javax.persistence.Table;
import java.util.Date;

@Data
@Table(name = "tb_user")
public class User{
    // id
    @Id
    //开启主键自动回填
    @KeySql(useGeneratedKeys = true)
    private Long id;

    // 用户名
    private String userName;

    // 密码
    private String password;

    // 姓名
    private String name;

    // 年龄
    private Integer age;

    // 性别，1男性，2女性
    private Integer sex;

    // 出生日期
    private Date birthday;

    // 创建时间
    private Date created;

    // 更新时间
    private Date updated;

    // 备注
    private String note;
}
```
<br>



### （五）创建数据表
&nbsp;  &nbsp;  &nbsp;  &nbsp; 根据需求创建数据表。
<br>



### （六）创建Mapper接口
&nbsp;  &nbsp;  &nbsp;  &nbsp; Mapper接口继承Mapper接口，并且要传入参数User对象。

```java
package com.user.mapper;

import com.user.pojo.User;
import tk.mybatis.mapper.common.Mapper;

public interface UserMapper extends Mapper<User> {
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 同时需要将该Mapper接口的全包名设置到引导类的注解@MapperScan的参数上面，用来扫描到这个Mapper包。
<br>



### （七）	创建service层的类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建service层的类来调用Mapper的方法，查询用户User的数据。本次使用的是Mapper中固有的方法，通过主键查询，而不是自定义的UserMapper类中的方法。

```java
package com.user.service;

import com.user.mapper.UserMapper;
import com.user.pojo.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    public User queryById(Long id){
        return userMapper.selectByPrimaryKey(id);
    }
}
```
<br>



### （八）.创建controller层的类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建UserController调用userService类的queryById方法，该方法的参数通过请求的地址中的id值来获取。

&nbsp;  &nbsp;  &nbsp;  &nbsp; @RestController是@Controller和@Responsebody的结合，代表着直接返回一个json字符串到页面上，而不是view；

&nbsp;  &nbsp;  &nbsp;  &nbsp; @GetMapping 表示着这是一个Get请求方法的@RequestMapping注解；

&nbsp;  &nbsp;  &nbsp;  &nbsp; @PathVariable 表示着从地址中取出某个参数；
<br>



```java
@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public User queryById(@PathVariable Long id){
        return userService.queryById(id);
    }
}
```
<br>



### （九）测试代码运行情况
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过引导类运行Spring程序，访问localhost:8080/user/9将数据库根据id=9索引出来的数据显示在页面上。
<br>



## 二．服务消费方实例
### （一）	需求
&nbsp;  &nbsp;  &nbsp;  &nbsp; 微服务消费方的需求是访问localhost:8081/consumer/8 使用RestTemplate获取localhost:8080/user/8的数据。
<br>



### （二）配置pom.xml
&nbsp;  &nbsp;  &nbsp;  &nbsp; 继承之前创建的父工程SpringCloudParentDemo，然后再添加一个web的启动依赖。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>com.SpringCloud.ParentDemo</artifactId>
        <groupId>com.SpringCloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>SpringCloudConsumer</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```
<br>



### （三）创建引导类和配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建一个引导类，同时需要在引导类中提前注册一个RestTemplate，以供调用。

```java
@SpringBootApplication
public class ConsumerApp {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApp.class, args);
    }

    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```
<br>



### （四）创建Controller
&nbsp;  &nbsp;  &nbsp;  &nbsp; 添加一个Controller，其中有函数queryBuId（）通过RestTemplate将微服务提供的服务数据截取过来，返回一个User对象，这个User对象与服务提供方的User对象是一样的。注意，User对象一定要设置get和set方法，不然会出错。

```java
@RestController
@RequestMapping("/consumer")
public class ConsumerController {
    @Autowired
    private RestTemplate restTemplate;

    public User queryBuId(Long id){
        String url = "http://localhost:8081/user/8";
        return restTemplate.getForObject(url, User.class);
    }
}
```
<br>



### （五）测试微服务消费方
&nbsp;  &nbsp;  &nbsp;  &nbsp; 启动引导类，访问localhost:8081/consumer/8，就会得到微服务提供方获取的数据库的数据，并显示在页面上。

<br>



## 三．总结项目的问题
### （一）服务管理
&nbsp;  &nbsp;  &nbsp;  &nbsp; 1. 服务如何自动注册和发现。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 2. 如何实现状态监管，服务提供方的状态和服务消费方的状态都是需要监测。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 3. 如何实现动态路由。
<br>



### （二）负载均衡
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当有多个服务器运行时，如何实现服务的负载均衡。

<br>


### （三）容灾问题
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当有服务器宕机时，如何解决容灾问题。
<br>



### （四）服务统一配置问题
&nbsp;  &nbsp;  &nbsp;  &nbsp; 若有多个服务器，如何对多个服务器进行统一的配置。
<br>
