## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	基本类型包装类
### （一）	定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;**将基本的数据类型封装成一个类，可以在该类中定义更多的功能方法来操作该数据类型。**
<br>

### （二）	常用操作
&nbsp;  &nbsp;  &nbsp;  &nbsp;基本数据类型与字符串之间的转换
<br>
### （三）包装类
<br>

基本数据类型 | 包装类
-------- | -----
byte |Byte
short |Short
int |Integer
long |Long
float |Float
double |Double
char |Character
boolean |Boolean

<br>


## 二．	Integer类
### （一）	定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;将原始类型int包装成类
<br>


### （二）	构造方法
#### 1.	public static Integer valueOf(int i)
&nbsp;  &nbsp;  &nbsp;  &nbsp;返回表示指定的 int 值的 Integer 实例

<br>

#### 2.	public static Integer valueOf(String s)
&nbsp;  &nbsp;  &nbsp;  &nbsp;返回一个保存指定值的 Integer 对象 String

<br>

### （三）	使用示例
<br>


```java
public class Integer类 {
    public static void main(String[] args) {
        Integer i1 = Integer.valueOf(100);
        System.out.println(i1);

        Integer i2 = Integer.valueOf("100");
        System.out.println(i2);
    }
}
```

<br>


## 三．	int和String类型的相互转换
### （一）	int转为String
<br>

#### 1.	int后直接加空字符串
<br>


#### 2.	String类的静态方法valueOf()
<br>


示例：

```java
public class int转String {
    public static void main(String[] args) {
        int num = 200;
        String s1 = num + "";
        System.out.println(s1);

        String s2 = String.valueOf(num);
        System.out.println(s2);
    }
}
```
<br>

### （二）	String转int类型
<br>

#### 1.	字符串转成Integer，在调用valueOf方法
<br>

#### 2.	Integer静态方法parseInt()
<br>

示例：

```java
public class String转int {
    public static void main(String[] args) {
        String s = "100";
        Integer i = Integer.valueOf(s);
        int x = i.intValue();
        System.out.println(x);

        int y = Integer.parseInt(s);
        System.out.println(y);
    }
}
```
<br>

## 四．	装箱和拆箱
### （一）装箱
&nbsp;  &nbsp;  &nbsp;  &nbsp;**将基本数据类型转换为对应的包装类类型**
<br>

### （二）拆箱
&nbsp;  &nbsp;  &nbsp;  &nbsp;**将包装类类型转换为对应的基本数据类型**
<br>

### （三）自动装箱与拆箱
&nbsp;  &nbsp;  &nbsp;  &nbsp;不需要调用方法，直接赋值操作即可。
<br>


### （四）示例
&nbsp;  &nbsp;  &nbsp;  &nbsp;


手动装箱：

```java
Integer i1 = Integer.value(100);
```

自动装箱：

```java
Integer i2 = 100;
```

手动拆箱：

```java
int i3 = i1.intValue() + 100;
```

自动拆箱：

```java
int i4 = i1 + 100;
```
&nbsp;  &nbsp;  &nbsp;  &nbsp;

**注意：在使用包装类时，需要进行判断null的操作。**





