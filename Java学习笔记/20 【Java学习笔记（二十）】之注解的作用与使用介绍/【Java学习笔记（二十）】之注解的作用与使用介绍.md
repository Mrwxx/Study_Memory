## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	注解
### （一）	概述

&nbsp;  &nbsp;  &nbsp;  &nbsp; 也叫元数据。一种代码级别的说明。它是JDK1.5及以后版本引入的一个特性，与类、接口、枚举是在同一个层次。它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明，注释。
<br>

### （二）	使用方法
&nbsp;  &nbsp;  &nbsp;  &nbsp; @注解名称
<br>

### （三）	注解的作用
#### 1.	编写文档
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过代码里标识的注解生成文档。
<br>

#### 2.	代码分析
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过代码里标识的注解对代码进行分析
<br>

#### 3.	编译检查
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过代码里标识的注解让编译器能够实现基本的编译检查（如@Override）

<br>

## 二．JDK内置注解
### 1.	@Override
&nbsp;  &nbsp;  &nbsp;  &nbsp; 检测该注解标注的方法是否是继承自父类的。
<br>

### 2.	@Deprecated
&nbsp;  &nbsp;  &nbsp;  &nbsp; 表示该注解标注的内容已经过时了，可以用但是不建议使用。
<br>

### 3. @SuppressWarnings
&nbsp;  &nbsp;  &nbsp;  &nbsp; 抑制警告，抑制已经显示的警告。一般传递参数all

```java
@SuppressWarnings(“all”)
```
<br>

## 三 自定义注解
### （一）	格式

```java
元注解
public @interface 注解名称{}
```
<br>

### （二）	注解的本质
&nbsp;  &nbsp;  &nbsp;  &nbsp; 注解本质上是一个接口，改接口默认继承Annotation接口
<br>

### （三）	注解的属性
&nbsp;  &nbsp;  &nbsp;  &nbsp; 注解中的属性即接口中的抽象方法，属性的返回值类型只能用以下的类型：

1.	基本数据类型
2.	String
3.	枚举
4.	注解
5.	以上类型的数组

&nbsp;  &nbsp;  &nbsp;  &nbsp; **定义了属性后，在使用注解时需要给属性赋值。即给抽象方法的返回值赋值。**
<br>

**注意：
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果使用default关键字给属性默认初始化值，则使用注解时可以不赋值属性。
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果只有一个属性要赋值，该属性名称为value，则value可省略直接定义值。**

<br>

## 四．元注解
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 预定义的注解，用于描述注解的注解。
<br>

### （二）举例
#### 1. @Target
&nbsp;  &nbsp;  &nbsp;  &nbsp; 用于描述注解能够作用的位置

```java
TYPE:作用于类上
METHOD:作用于方法上
FIELD:作用于成员变量上
```

<br>

#### 2．@Retention
&nbsp;  &nbsp;  &nbsp;  &nbsp; 描述注解被保留的阶段，三个阶段：源代码夹断，Class类阶段，运行阶段。

```java
@Retention(RetentionPolicy.SOURCE)
@Retention(RetentionPolicy.CLASS)
@Retention(RetentionPolicy.RUNTIME)
```
<br>

#### 3.	@Documented
&nbsp;  &nbsp;  &nbsp;  &nbsp; 描述注解是否被抽取到api文档中显示。
<br>

#### 4.	@Inherited
&nbsp;  &nbsp;  &nbsp;  &nbsp; 描述注解是否被子类继承

<br>

## 五．注解的使用
&nbsp;  &nbsp;  &nbsp;  &nbsp; 注解通常用于取代配置文件，通过解析注解即可获取注解中定义的属性。
<br>

### （一）	定义注解：

```java
@Target({Element.Type.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Pro{
	String className();
	String methodName();
}
```
<br>

### （二）	定义类

```java
@Pro(className = “xx.xx”, methodName = “xx”)
public static class ReTest{}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们要在ReTest类中利用注解取得相应的className和methodName，需要通过以下的步骤来获取。
<br>

#### 1.	解析注解
&nbsp;  &nbsp;  &nbsp;  &nbsp; 获取该类的字节码文件对象

```java
Class<ReTest> ret = ReTest.class;
```
<br>

#### 2.	获取注解定义位置的对象
&nbsp;  &nbsp;  &nbsp;  &nbsp; 获取注解对象就是在内存中生成了该注解接口的子类实现对象。

```java
Pro an = ret.getAnnotation(Pro.class)
```
<br>

#### 3.	调用注解对象中定义的抽象方法，获取返回值

```java
String className = an.className();
String methodName = an.methodName();
```
<br>

## 六．注解用于搭建简单的测试框架
### （一）需求
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将注解加于方法之上，即可测试该方法是否会出现异常，并将异常记录在文件中。
<br>

### （二）代码
&nbsp;  &nbsp;  &nbsp;  &nbsp; 测试计算器类Calculator中的方法是有会出现异常，将@Check注解放置于方法上，有注解的方法被测试类TestCheck测试，测试结果放在bug.txt文本文件中。
<br>

**每个文件的代码如下：**

#### 1. 计算器类Calculator

```java
package 注解实现测试框架;

public class Calculator {
    //加法
    @Check
    public void add(){
        System.out.println("1 + 0 =" + (1 + 0));
    }

    //减法
    @Check
    public void sub(){
        System.out.println("1- 0 = " + (1-0));
    }

    //除法
    @Check
    public void div(){
        System.out.println("1 / 0 = " + (1 / 0));
    }
}
```

<br>

#### 2.自定义注解@Check
<br>


```java
package 注解实现测试框架;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Check {

}
```
<br>

#### 3.测试类
<br>


```java
package 注解实现测试框架;

import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class TestCheck {
    public static void main(String[] args) throws IOException {
        Calculator c = new Calculator();
        //获取该类的字节码文件对象
        Class<? extends Calculator> cls = c.getClass();
        //获取所有方法
        Method[] methods = cls.getMethods();

        int num = 0;
        BufferedWriter bw = new BufferedWriter(new FileWriter("bug.txt"));

        for(Method method : methods){
            //判断方法上是否有Check注解
            if(method.isAnnotationPresent(Check.class)){
                //有Check注解，执行
                try{
                    method.invoke(c);
                } catch (Exception e) {
                    //捕获异常，记录到文件中

                    num++;
                    bw.write(method.getName() + "方法出现异常");
                    bw.newLine();
                    bw.write("异常的名称：" + e.getCause());
                    bw.newLine();
                    bw.write("异常的原因：" + e.getMessage());
                    bw.newLine();
                    bw.write("----------------");
                    bw.newLine();
                }
            }
        }

        bw.write("本次测试一共出现" + num + "次异常");

        bw.flush();
        bw.close();

    }
}
```







