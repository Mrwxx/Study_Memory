## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	数据库连接池
### （一）	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;数据库连接池是一个用于存放数据库连接的容器。当系统初始化后，数据库连接池被创建，它会申请一些连接对象，当用户访问数据库时，，会从连接池中获取连接对象用于连接数据库；当访问结束时，将连接对象返回给数据库连接池。
<br>


### （二）	优势
 &nbsp;  &nbsp;  &nbsp;  &nbsp;访问数据库效率提高，且节约了资源。
<br>



### （三）	实现
#### 1.	接口
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Javax.sql包下的DataSource接口。
<br>



#### 2.	方法
 &nbsp;  &nbsp;  &nbsp;  &nbsp;从数据库连接池中获取连接： getConnection()
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;将连接归还到数据库连接池：Connection.close()，直接用close()就可以将连接归还到连接池。
<br>



#### 3.	数据库连接池示例
 &nbsp;  &nbsp;  &nbsp;  &nbsp;C3P0： 数据库连接池技术
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Druid:  阿里的数据库连接池技术

<br>



## 二．	C3P0数据库连接池示例
### （一）	步骤
 &nbsp;  &nbsp;  &nbsp;  &nbsp;1.	在项目中创建lib目录，导入jar包，c3p0.jar，mchange-commons-java.jar和数据库驱动jar包。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;2.	添加配置文件 c3p0.properties或者c3p0.xml，将配置文件直接放到src目录下即可。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;3.	创建数据库连接池对象 ComboPooledDataSource对象。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;4.	获取连接 getConnection()
<br>



### （二）	配置文件参数
#### 1.	默认配置
<br>



```javascript
<default-config>
   <!--  连接参数 -->
  <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
  <property name="jdbcUrl">jdbc:mysql://localhost:3306/heima</property>
  <property name="user">root</property>
  <property name="password">123</property>
  
  <!-- 连接池参数 -->
<!--  初始连接对象数 -->
  <property name="initialPoolSize">5</property>
   <!--  最大连接对象数 -->
<property name="maxPoolSize">10</property>
   <!--  超时时间 -->
<property name="checkoutTimeout">3000</property>
</default-config>
```
<br>



#### 2.	指定配置名
<br>



```javascript
<named-config name="otherc3p0"> 
  <!--  连接参数 -->
  <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
  <property name="jdbcUrl">jdbc:mysql://localhost:3306/heima</property>
  <property name="user">root</property>
  <property name="password">123</property>
  
  <!-- 连接池参数 -->
  <property name="initialPoolSize">5</property>
  <property name="maxPoolSize">8</property>
  <property name="checkoutTimeout">1000</property>
</named-config>
```
<br>

## 三．	Druid连接池技术的使用
### （一）步骤
 &nbsp;  &nbsp;  &nbsp;  &nbsp;1.	导入druid.jar包
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;2.	定义配置文件，xx.properties，可放在任意目录下。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;3.	加载配置文件，可将配置文件放在src目录下，通过classLoader()来获取该配置文件并加载。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;4.	获取数据库连接池对象，通过DruidDataSourceFactory()获取。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;5.	获取连接，通过getConnection()获取。
<br>



### （三）	连接池工具类
<br>



```java
package utils;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JDBCUtils {

    private static DataSource ds;
    static{

        try {
            //加载配置文件
            Properties pro = new Properties();
            pro.load(JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties"));
            //获取连接池对象
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //获取连接 方法
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

    //释放资源
    public static void close(Statement stm, Connection con){
        if(stm != null){
            try {
                stm.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if(con != null){
            try {
                con.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    public static void close(ResultSet rs, Statement stm, Connection con){
        if(rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if(stm != null){
            try {
                stm.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if(con != null){
            try {
                con.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    //获取连接池方法
    public static DataSource getDataSource(){
        return ds;
    }
}
```
<br>



## 四．	JdbcTemplate
### （一）	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Spring框架提供了JDBC的简单封装，提供了一个JDBCTemplate对象简化了JDBC的开发。不需要自己创建连接，释放资源，只需要专注于需要执行的SQL语句，其他的交给JdbcTemplate完成。
<br>



### （二）	方法
 &nbsp;  &nbsp;  &nbsp;  &nbsp;1.	update():执行DML语句，增删改操作
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;2.	queryForMap(): 将查询结果集封装为map集合，列名为key，值为value，只能查询一条结果，封装为一个Map集合
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;3.	queryForList(): 将查询结果集封装为list集合，将多条记录封装为map集合，再将map结合装到list集合中
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;4.	query():将查询结果封装为JavaBean对象，参数为RowMapper，我们一般使用BeanPropertyRowMapper实现类，可以完成数据到JavaBean的自动封装：
 

```java
new BeanPropertyRowMapper<类型>(类型.class)
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;5.	queryForObject():将查询结果封装为一般对象
<br>



### （三）	步骤
 &nbsp;  &nbsp;  &nbsp;  &nbsp;1.	导入spring-beans.jar，spring-core.jar，spring-jdbc.jar，sprin-tx.jar，commons-logging,jar包
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;2.	创建连接池对象DataSource，然后创建JdbcTemplate对象
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;3.	调用JdbcTemplate()的方法完成增删改查
<br>



### （四）	代码实现
<br>



```java
package SprintTets;

import org.springframework.jdbc.core.JdbcTemplate;
import utils.JDBCUtils;

public class test {
    public static void main(String[] args) {
        //创建JDBCTemplate对象
        JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
        //调用方法
        String sql = "update student set age = 12 where id = 12";
        int count = template.update(sql);
        System.out.println(count);
    }
}
```







