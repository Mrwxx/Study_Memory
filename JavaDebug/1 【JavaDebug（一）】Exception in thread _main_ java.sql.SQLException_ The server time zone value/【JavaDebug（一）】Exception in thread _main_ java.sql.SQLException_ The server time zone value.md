## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 错误概况
<br>


```sql
package JDBC;

import java.io.IOException;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class test {
    public static void main(String[] args) throws IOException, SQLException {

        String url = "jdbc:mysql://localhost:3306/h";
        Connection con = DriverManager.getConnection(url, "root", " ");
        System.out.println(con);
    }
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;JDBC链接数据库时，出现以上错误，可以由错误提示大概了解，服务器的server time值有问题，也就是时间设置的问题。

&nbsp;  &nbsp;  &nbsp;  &nbsp;因此，我们到mysql中查看数据库的时间设置。
<br>



## 二. 解决问题
&nbsp;  &nbsp;  &nbsp;  &nbsp;在Mysql中，我们查找time相关的参数：

```sql
show variables like '%time_zone%';
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;找出了time_zone参数对应的值为SYSTEM，该值对应的是美国时间，我们的时区为东八区，应该改为'+8:00'。

```sql
set global time_zone='+8:00';
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;修改完成后，需要重新登陆mysql，改变才会生效。


