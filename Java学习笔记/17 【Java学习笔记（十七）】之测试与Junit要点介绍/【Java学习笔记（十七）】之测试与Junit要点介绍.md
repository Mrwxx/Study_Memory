## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	测试
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们写完程序后都会对程序的逻辑功能进行测试，看看程序是否有缺陷。通过测试，我们可以找出缺陷，完善程序的逻辑。
<br>

### （二）	测试分类
&nbsp;  &nbsp;  &nbsp;  &nbsp;测试分为黑盒测试和白盒测试，黑盒测试只需要输入数据，得出输出值，而不关注具体的执行过程；白盒测试需要关注具体的执行过程，通过写代码来测试。
<br>

## 二．	Junit
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;测试单元，当我们自己对程序进行测试时，都要重写一个测试类，且需通过main函数来测试。Junit单元允许我们只测试一个单元，如测试一个方法，测试一个类，这样测试就非常地轻便，简洁。
<br>

### （二）	Junit使用方法
#### 1.	定义测试类
&nbsp;  &nbsp;  &nbsp;  &nbsp;建议将测试类都放在一个test包中，每个测试类的类名按照：被测试类+Test 的格式创建。
<br>

#### 2.	定义测试方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;测试类中的方法，测试方法名按照 test+方法名 的格式创建，一般不需返回值，也不需要参数，因为只是用于测试方法的正确性。
<br>

#### 3.	为测试方法添加注解@Test，并导入Junit依赖环境。

<br>

#### 4.	在测试方法中添加测试代码
&nbsp;  &nbsp;  &nbsp;  &nbsp;一般使用**断言操作来判断结果**，断言即判断正确结果与真实结果是否匹配：

```java
Assert.assertEquals(期望，真实)
```
<br>

### （三）Junit使用代码示例： 
<br>

```java
package test;

import org.junit.Assert;
import org.junit.Test;
import 测试.Calculator;

public class CalculatorTest {

    @Test
    public void testAdd(){
        Calculator c = new Calculator();
        int res = c.add(1, 2);
       // System.out.println(res);

        //断言
        Assert.assertEquals(3, res);
    }
}
```
<br>

### （四）注解
#### 1. @Before
&nbsp;  &nbsp;  &nbsp;  &nbsp;测试类中一般在测试方法执行之前都要申请资源，即在测试方法之前执行，可以用注解@Before来表示。
<br>

```java
@Before
public void init(){
    System.out.println("init");
}
```
<br>

#### 2、@After
&nbsp;  &nbsp;  &nbsp;  &nbsp;在测试之后，需要释放资源，用注解@After来表示在测试代码后执行的代码。
<br>

```java
@After
public void close(){
    System.out.println("close");
}
```
<br>

#### 3. @BeforeClass
&nbsp;  &nbsp;  &nbsp;  &nbsp;针对所有的测试，只执行一次，且必须为static void,优先级最高。
<br>

#### 4.@AfterClass
&nbsp;  &nbsp;  &nbsp;  &nbsp;针对所有的测试，只执行一次，且必须为static void,在最后执行。
<br>

#### 5.	@Ignore
&nbsp;  &nbsp;  &nbsp;  &nbsp;忽略的测试方法
<br>

### （五）单元测试用例的执行顺序
<br>

**&nbsp;  &nbsp;  &nbsp;  &nbsp;@BeforeClass –> @Before –> @Test –> @After –> @AfterClass** 




