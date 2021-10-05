## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	Date类
<br>

### （一）	定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;代表了一个特定的时间，精确到毫秒
<br>

### （二）	构造方法
<br>

#### 1.	public Date()
&nbsp;  &nbsp;  &nbsp;  &nbsp;构造并初始化一个Date对象，**表示该对象创建时的时间，精确到毫秒**
<br>


#### 2.	public Date(long date)
&nbsp;  &nbsp;  &nbsp;  &nbsp;**构造并初始化一个Date对象，传入参数date，将其初始化为标准基准时间延后date毫秒的日期**。标准基准时间为1970年7月1日0:00，中国时区为东8区，因此起始时间为1970年7月1日8:00。

示例：

```java
public class Date类 {
    public static void main(String[] args) {
        Date d1 = new Date();
        System.out.println(d1);

//        long date = 1000 * 60 * 60*60;
        long date = 0;
        Date d2 = new Date(date);
        System.out.println(d2);
    }
}
```
<br>

### （三）	Date类常用方法
<br>

#### 1.	Public long getTime()
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取该日期对象从标准起始时间到现在的毫秒值。
<br>

#### 2.	Public void setTime(long time)
&nbsp;  &nbsp;  &nbsp;  &nbsp;传入毫秒值time,设置时间为从标准起始时间开始偏移time毫秒数的时间。
<br>

示例：

```java
public class Date类 {
    public static void main(String[] args) {
        Date d1 = new Date();
        System.out.println(d1);

//        long date = 1000 * 60 * 60*60;
        long date = 0;
        Date d2 = new Date(date);
        System.out.println(d2);

        System.out.println(d2.getTime());
        //long time = System.currentTimeMillis();
        long time = 1000 * 60 * 60;
        d2.setTime(time);
        System.out.println(d2);
    }
}
```
<br>

## 二．	SimpleDateFormat类
&nbsp;  &nbsp;  &nbsp;  &nbsp;**该类用于设置日期的具体格式和解析日期，相当于该类只是用于设置日期的格式。
<br>**

### （一）	构造方法
#### 1.	Public SimpleDateFormat()
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用默认模式和日期格式
<br>


#### 2.	Public SimpleDateFormat(String pattern)
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用给定的日期格式
<br>


### （二）	常用方法
<br>

#### 1.	格式化
&nbsp;  &nbsp;  &nbsp;  &nbsp;从Date对象转换为String对象，将日期格式化为时间字符串

```java
public final String format(Date date)
```
<br>

#### 2.	解析
&nbsp;  &nbsp;  &nbsp;  &nbsp;从String对象转换为Date对象，将时间字符串转换为日期对象

```java
public Date parse(String source)
```
<br>

### （三）	示例
<br>


```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class SimpleDateFormat类 {
    public static void main(String[] args) throws ParseException {
        Date d = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
        String s = sdf.format(d);
        System.out.println(s);

        String ss= "2012-01-01 11:11:11";
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date dd = sdf2.parse(ss);
        System.out.println(dd);
    }
}
```







