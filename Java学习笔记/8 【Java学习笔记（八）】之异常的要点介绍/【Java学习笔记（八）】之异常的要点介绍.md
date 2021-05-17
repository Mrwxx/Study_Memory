## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	异常
### （一）	定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;程序出现了不正常的情况，程序可以捕获到异常，并且处理这个异常。
<br>


### （二）	异常体系结构
&nbsp;  &nbsp;  &nbsp;  &nbsp;**Throwable类是所有错误和异常的父类。错误指的是程序出现的非常严重的问题，程序无法解决。异常是程序可以捕获的轻微的问题，我们可以通过捕获异常解决这个问题。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;异常分为 RuntimeException和非RuntimeException。RuntimeException是指在运行阶段才会检查的异常，非RuntimeException是指在编译阶段就会检查的异常。
<br>

### （三）	JVM默认处理异常的方式
&nbsp;  &nbsp;  &nbsp;  &nbsp;输出异常名称，错误原因以及异常出现的位置在控制台，并终止程序的执行。

<br>

## 二．	异常的处理
<br>

### （一）	try-catch
<br>


```java
try {
	可能出现异常的代码;
} catch(异常类名 变量名) {
	异常的处理代码;
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 程序先进入try代码块中，进行正常逻辑处理，这也是异常可能出现的地方；

&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 当异常出现时，会生成一个异常类对象；

&nbsp;  &nbsp;  &nbsp;  &nbsp;3. 接着，catch中会匹配到该异常类，进入catch代码块中进行异常的处理；

&nbsp;  &nbsp;  &nbsp;  &nbsp;4. 异常处理结束后，程序接着往下执行。
<br>

```java
public class ExceptionDemo01 {
  public static void main(String[] args) {
    System.out.println("start");
    method();
    System.out.println("end");
 }
  public static void method() {
    try {
      int[] arr = {1, 2, 3};
      System.out.println(arr[3]);
   } catch (ArrayIndexOutOfBoundsException e) {
//      System.out.println("索引越界");
      e.printStackTrace();
   }
 }
}
```
<br>

### （二）	throws
<br>

#### 1.	定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;**跟在方法声明的括号后面，表示抛出异常，但只是表示这种异常可能会出现，而异常的处理需要由方法的调用者来处理。**
<br>

#### 2.	格式

```java
public void 方法() throws 异常类名{}
```
<br>

#### 3.	示例
```java
public class ExceptionDemo {
  public static void main(String[] args) {
    try {
      method();
   }catch (ParseException e) {
      e.printStackTrace();
   }
 }
  //编译时异常
  public static void method() throws ParseException {
    String s = "2048-08-09";
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
    Date d = sdf.parse(s);
    System.out.println(d);
 }
}
```
<br>

#### 4.	throws 和 throw的区别
<br>

##### (1)	throw
&nbsp;  &nbsp;  &nbsp;  &nbsp;**throw是用在方法体内的，抛出的是异常对象，表示抛出异常，且由方法体内进行处理。当执行throw时说明一定抛出了异常。**
<br>

##### (2)	throws
&nbsp;  &nbsp;  &nbsp;  &nbsp;throws表示抛出异常类名，但只是表示异常可能出现，如果出现，则有方法的调用者来处理异常。

<br>

## 三．	异常的区别
<br>

### （一）	编译时异常
&nbsp;  &nbsp;  &nbsp;  &nbsp;**Exception类及其子类**，必须显示处理，如用try-catch或者throws抛出异常，否则程序会报错，无法通过编译。
<br>


### （二）	运行时异常
&nbsp;  &nbsp;  &nbsp;  &nbsp;**RuntimeException类及其子类**，可以不用显示处理。





