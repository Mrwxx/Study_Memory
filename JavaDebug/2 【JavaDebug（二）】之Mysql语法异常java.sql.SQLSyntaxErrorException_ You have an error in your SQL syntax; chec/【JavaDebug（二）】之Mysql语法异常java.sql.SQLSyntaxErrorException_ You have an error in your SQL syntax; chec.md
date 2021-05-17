## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 异常
### （一）异常描述

```java
java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''id',name,age) values(14,'dd',24)' at line 1
```
<br>


### （二）解决问题
&nbsp;  &nbsp;  &nbsp;  &nbsp;在用JDBC连接Mysql时，总是报异常，通过查看异常提示，可以看到是SQL的语法问题：

```java
 right syntax to use near ‘‘id’,name,age) 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我尝试着将字段名称里的单引号都消去了，结果没有报异常。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，异常提示中 near ' 已经提示我们了是单引号的问题！！！**
<br>



## 二. 代码
<br>



```java
package JDBC;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class InsertTest {
    public static void main(String[] args) {
        Statement stm = null;
        Connection con = null;

        try {
            //1.注册驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //2.定义SQL
            String sql = "insert into student(id,name,age) values(14,'dd',24)";
            //3.获取Connection对象
            con = DriverManager.getConnection("jdbc:mysql:///heima", "root", " ");
            //4.获取执行SQL的对象Statement
            stm = con.createStatement();
            //5.执行SQL
            int count = stm.executeUpdate(sql);
            //6.处理结果
            System.out.println(count);
            if(count > 0){
                System.out.println("添加成功！");
            }
            else{
                System.out.println("添加失败！");
            }
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }finally {
            //释放资源
            //判断空指针异常
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

    }
}
```



