## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	MyBatis
### （一）	原始JDBC操作的分析
#### 1.	原始JDBC开发存在的问题：
 &nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	数据库连接创建，释放频繁造成系统资源浪费从而影响系统性能。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	sql语句在代码中硬编码，造成代码不易维护，实际应用SQL的改动需要改变java代码
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	查询操作时，需要手动将结果集中的数据封装到实体中；插入操作时，需要手动将实体的数据设置到sql语句的占位符的位置。
<br>


#### 2.	解决方案
 &nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	使用数据库连接池初始化连接资源，解决资源浪费问题。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	将SQL语句抽取到xml配置文件中。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	使用反射，内省等技术，自动将实体与表进行属性与字段的自动映射。
<br>



### （二）	Mybatis简介
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Mybatis是一个优秀的基于java的持久层框架，它内部封装了jdbc，使开发者只需要关注sql语句本身，而不需要花费精力去处理加载驱动，创建连接，创建statement等繁杂的过程。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Mybatis通过xml或注解的方式将要执行的各种statement配置起来，并通过java对象和statement中sql的动态参数进行映射生成最终执行的sql语句。Mybatis框架执行完sql语句后，将结果映射为java对象返回，这是采用了ORM（Object Relation Mapping）对象关系映射思想来解决实体对象和数据库的映射问题。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Mybatis对jdbc进行了封装，屏蔽了jdbc api底层访问的细节，使得我们不用与jdbc api打交道，即可完成对数据库的持久化操作。
<br>




## 二．	Mybatis的开发步骤
### （一）	添加Mybatis的坐标
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在pom.xml中添加Mybatis的坐标，数据库驱动，测试，日志。
<br>



### （二）	创建数据库表user
 &nbsp;  &nbsp;  &nbsp;  &nbsp;为该数据表添加三个字段，id，username，password。
<br>



### （三）	编写实体类User
 &nbsp;  &nbsp;  &nbsp;  &nbsp;根据数据表，创建一个实体类User，属性有id，username，password。
<br>



### （四）	编写映射文件UserMapper.xml
 &nbsp;  &nbsp;  &nbsp;  &nbsp;命名规则由实体对象+Mapper组成，位置在实体类的resources目录下。这个文件中写的是SQL语句。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper>
    <select id="findAll" resultType="com.mybatis.domain.User">
        select * from user
    </select>
</mapper>
```
<br>



### （五）	编写核心文件SqlMapConfig.xml
 &nbsp;  &nbsp;  &nbsp;  &nbsp;该文件用于配置Mybatis。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

<!--    数据源环境-->
    <environments default="developement">
        <environment id="developement">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```
<br>



### （六）	编写测试类

```xml
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MyBatisTest {

    @Test
    public void test1() throws IOException {
        //获得核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
        //获得session工厂对象
        SqlSessionFactory sqlSessionFactory =  new SqlSessionFactoryBuilder().build(resourceAsStream);
        //获得session会话对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //执行操作， 参数是 namespace + id
        List<Object> userList = sqlSession.selectList("userMapper.findAll");
        System.out.println(userList);
        //释放资源
        sqlSession.close();
    }
}
```
<br>



## 三．	MyBatis的映射文件
 &nbsp;  &nbsp;  &nbsp;  &nbsp;映射文件即为上面的UserMapper.xml配置文件，下面来详解该配置文件。

### （一）	约束头
 &nbsp;  &nbsp;  &nbsp;  &nbsp;给配置文件中的约束头既能够起约束作用，也会有提示作用。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```
<br>



### （二）	根标签mapper

```xml
<mapper namespace=”userMapper”>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;根标签为mapper，有一个属性是namespace命名空间，与下面的sql语句的id一起组成调用语句的标识。
<br>



### （三）	操作子标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在根标签mapper内部有操作的子标签，如同SQL语句的增删改查操作一样，子标签使用的就是insert(增)，delete(删)，update（改），select（查）。属性id是该SQL语句的id名称，与根标签的命名空间一起组成查询的标识，如userMapper.findAll。属性resultType是和操作的数据表的数据进行映射的实体类的全类名，操作数据表后能够将数据封装到该实体类中。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;再下面就是一个SQL语句。
 
如，查询操作

```xml
<mapper namespace=”userMapper”>
	<select id=”findAll” resultType=”com.mybatis.domain.User”>
	select * from User
</mapper>
```

 <br>



### （四）	查询数据操作
 &nbsp;  &nbsp;  &nbsp;  &nbsp;要从数据库中查询数据，并封装到对象实体中。因此，在映射配置文件UserMapper.xml中，我们需要在select子标签中设置resultType属性，值为实体对象的全类名。

```xml
<mapper namespace =”userMapper”>
	<select id=”findAll” resultType=”domain.User”>
		select * from user
	</select>
</mapper>
```
<br>



### （五）	插入数据操作
 &nbsp;  &nbsp;  &nbsp;  &nbsp;不同于查询数据操作，插入数据操作需要从实体对象中抽取属性的值到sql语句中，进行插入操作。那么，如何从实体对象中抽取属性值呢？
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在映射文件UserMapper.xml中设置insert子标签，并设置属性parameterType，值为要抽取的实体对象的全类名。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;下面的sql语句如何写呢？如何将实体对象中的每个属性写到SQL语句中？
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过#(属性名)的方式，代表着抽取该对象的属性名。

```xml
<mapper namespace =”userMapper”>
	<insert id=”save” parameterType=”domain.User”>
		insert into user values(#{id}, #{username}, #{password})
</insert>
</mapper>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;插入操作使用的API是`sqlSession.insert(“命名空间.id”，实体对象);`
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;插入操作设计是数据库数据的变化，所以要使用sqlSession对象显式地提交事务，如sqlSession.commit()，数据才会被提交到数据库中，一般更新数据库的操作都需要显式地提交事务。
<br>



### （六）	修改数据操作
 &nbsp;  &nbsp;  &nbsp;  &nbsp;修改操作和插入操作类似，在映射文件UserMapper.xml中都需要抽取实体类对象的数据，因此都需要parameterType属性。同样，在测试类中使用时最后都需要提交事务。

```xml
<insert id=”save” parameterType=”domain.User”>
<update id=”update” parameterType=”domain.User”>
		update user set username=#{username}, password=#{password} where id=#{id}
</update>
</insert>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;修改操作使用的API是sqlSession.update(“命名空间.id”, 实体对象)
<br>



### （七）	删除数据操作
 &nbsp;  &nbsp;  &nbsp;  &nbsp;删除操作，使用delete子标签，属性parameterType值为要删除的值的类型全类名，如int类型的全类名为java.lang.Integer。其他的使用方式是一样的。

如，根据id值来删除：

```xml
<mapper namespace=”userMapper”>
		<delete id=”delete” parameterType=”java.lang,Integer”>
		delete from user where id=#{id}
</mapper>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;调用时，使用delete函数，7是要删除的int类型值： 

```java
sqlSession.delete(“userMapper.delete”, 7);
```

<br>



## 四．	MyBatis核心配置文件
### （一）	约束头
 &nbsp;  &nbsp;  &nbsp;  &nbsp;每个MyBatis核心配置文件上都有一个约束头。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
```
<br>



### （二）	configuration根标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;跟在约束头后面的就是configuration根标签。

```xml
<configuration></configuration>
```
<br>



### （三）	environments标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;environments标签是对数据库环境的配置，在该标签内还可以有多个environment子标签，即支持多个环境的配置。
#### 1.	default属性
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在environments标签的default属性中，可以指定默认的环境名称。

如，指定id名为 development的环境：

```xml
<environments default=”development”>
```
<br>



#### 2.	environment 标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;每个环境的id属性是自己的标识，指定当前环境的名称：

```xml
<environment id=”development”>
```
<br>



#### 3.	transactionManager标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;该标签为事务管理， type属性可以指定事务管理的类型。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;有两种类型，一种是JDBC，这个配置就是直接使用了JDBC的提交和回滚设置，依赖于从数据源得到的连接来管理事务的作用域。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;第二种是MANAGED，这个配置的意思是让容器来管理事务的整个过程，他不会提交或回滚一个连接。默认情况下它会关闭连接，通常我们需要将closeConnection属性设置为false来阻止它。

如，指定类型为JDBC:

```xml
<transactionManager type=”JDBC”>
```
<br>



#### 4.	dataSource标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;该标签用于设置当前的数据源，type属性可以指定数据源的类型。

数据源的类型共有三种：

（1）	UNPOOLED
 &nbsp;  &nbsp;  &nbsp;  &nbsp;这个数据源不是连接池类型，每次被请求都要打开和关闭连接，效率低。

（2）	POOLED
 &nbsp;  &nbsp;  &nbsp;  &nbsp;数据源为连接池类型。

（3）	JDNI
 &nbsp;  &nbsp;  &nbsp;  &nbsp;这个数据源的实现是为了能在EJB服务器这类容器中使用，容器可以集中火灾外部配置数据源，然后放置一个JNDI上下文的引用。

如连接池类型：

```xml
<dataSource type=”POOLED”>
```
<br>



#### 5.	property标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;property标签是dataSource标签的子标签，用于配置数据源的基本信息，如驱动，url，用户名，密码：

```xml
<property name=”driver” value=”${jdbc.driver}”/>
<property name=”url” value=”${jdbc.url}”/>
<property name=”username” value=”${jdbc.username}”/>
<property name=”password” value=”${jdbc.password}”/>
```

一个environments标签体内部的整体结构如下所示：

```xml
<environments default=”development”>
	<environment id=”development”>
		<transactionManager type=”JDBC”/>
		<dataSource type=”POOLED”>
			<property name=”driver” value=”${jdbc.driver}”/>
			<property name=”url” value=”${jdbc.url}”/>
			<property name=”username” value=”${jdbc.username}”/>
			<property name=”password” value=”${jdbc.password}”/>
		</dataSource>
	</environment>
</environments>
```
<br>



### （四）	mappers标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;我们在测试MyBatis的测试类中，只是调用了MyBatis核心配置文件，并没有调用映射文件，因为在核心配置文件的mappers标签就是为了加载映射文件。加载方式又如下几种：
#### 1.	使用相对类路径的资源引用
 &nbsp;  &nbsp;  &nbsp;  &nbsp;即映射文件的全包名。
如：

```xml
<mapper resource=”org/mybatis/userMapper.xml”/>
```
<br>



#### 2.	使用完全限定资源定位符（URL）
 &nbsp;  &nbsp;  &nbsp;  &nbsp;这种就是直接指定映射文件所在的位置。
如：

```xml
<mapper url=”file://var/mappers/userMapper.xml” />
```
<br>



#### 3.	使用映射器接口实现类的完全限定类名
 &nbsp;  &nbsp;  &nbsp;  &nbsp;使用注解时会接触到，class属性为全类名。
如：

```xml
<mapper class=”org.mybatis.builer.userMapper”/>
```
<br>



#### 4.	将包内的映射器接口实现全部注册为映射器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;这与上面的不同在于，可以将包内的所有映射器都注册为映射器，而上面只能使用一个映射器。
如：

```xml
<package name=”org.mybatis”/>
```
<br>



### （五）	properties标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;将配置信息抽取到一个properties文件中，是一个非常高效的方法。在MyBatista的核心配置文件中，数据库环境的配置就需要使用外部的properties文件。在使用前设置properties标签，resource属性为properties文件的位置。

如：

```xml
<properties resource=”jdbc.properties”></properties>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;然后，在使用properties文件中的键值对时，通过EL表达式的形式取出键值对的值。

如：

```xml
<property name=”driver” value=”${jdbc.driver}”/>
```
<br>



### （六）	typeAliases标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;此标签可以为一个类型设置一个别名，使开发更加高效。一些简单类型MyBatis已经为它们设置了别名，如：

数据类型	|别名
-|-
String 		|string
Long	|long
Integer |int
Double| double
Boolean| Boolean

那没有预定义别名的如何自定义别名呢？

 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过typeAliases标签来定义，可以在其中用typeAlias子标签定义多个别名type属性是类名，alias属性是别名：

```xml
<typeAliases>
	<typeAlias type=”com.mybatis.domain.User” alias=”user”></typeAlias> 
</typeAliases>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;这样，我们在使用该类时就可以使用别名了。
<br>




## 五．	MyBatis相应的API
### （一）	SqlSessionFactory构建器SqlSessionFactoryBuilder
 &nbsp;  &nbsp;  &nbsp;  &nbsp;我们要通过SqlSessionFactoryBuilder这个工厂构建器来创建SqlSessionFactory。这是通过工厂构建器的build函数实现的：

```java
SqlSessionFactory build(InputStream inputStream);
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;首先通过Resources工具类将MyBatis的核心文件生成输入流的格式，再创建一个SqlSessionFactoryBuilder，通过这个构建器的build函数生成一个SqlSessionFactory工厂。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;Resources工具类在org.apache.ibatis.io 包中，它能帮助从类路径下，文件系统或一个web url中加载资源文件。

```java
String resources = “org/mybatis/builder/mybatis-confg.xml”;
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(inputStream);
```
<br>



### （二）	SqlSessionFactory工厂
 &nbsp;  &nbsp;  &nbsp;  &nbsp;如名字的意思，SqlSessionFactory是SqlSession的工厂对象，能够创建SqlSession对象，创建的方式有两种：
#### 1.	openSession()
 &nbsp;  &nbsp;  &nbsp;  &nbsp;SqlSessionFactory对象调用函数openSession()，没有任何参数，会创建一个SqlSession对话实例，但同时也会默认开启一个事务，且这个事务不会自动提交，需要我们手动提交这个事务后，更新操作的数据才会持久化到数据库中。
<br>



#### 2.	openSession(boolean autoCommit)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;有一个参数autoCommit，可设置是否自动提交事务，若为true，则无需手动提交事务。
<br>



### （三）	SqlSession会话对象
 &nbsp;  &nbsp;  &nbsp;  &nbsp;SqlSession会话对象可以用于执行SQL语句，提交或回滚事务，获取映射器实例。
#### 1.	执行SQL语句

```java
<T> T selectOne(String statement, Object parameter);
<E> List<E> selectList(String statement, Object parameter);
int insert(String statement, Object parameter);
int update(String statement, Object parameter);
int delete(String statement, Object parameter);
```
<br>



#### 2.	操作事务

```java
void commit();
void rollback();
```







