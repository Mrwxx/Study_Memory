## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．Lambda表达式的构成
&nbsp;  &nbsp;  &nbsp;  &nbsp; C++11中的Lambda表达式用于定义并**创建匿名的函数对象**，以简化编程工作。

&nbsp;  &nbsp;  &nbsp;  &nbsp; Lambda**表达式的构成**：

```cpp
[函数对象参数](操作符重载函数参数)mutable ->返回值{函数体}
```
<br>

## 二．函数对象参数
&nbsp;  &nbsp;  &nbsp;  &nbsp; 【】函数对象参数是传递给编译器自动生成的函数对象类的构造函数的。**函数对象参数只能使用那些到定义Lambda为止时Lambda所在作用范围内可见的局部变量（包括Lambda所在类的this）**。有多种形式：

### （一）	无参数
&nbsp;  &nbsp;  &nbsp;  &nbsp; 没有参数
<br>

### （二）	=
&nbsp;  &nbsp;  &nbsp;  &nbsp; **通过值传递的方式使用到定义Lambda为止时Lambda所在作用范围内可见的局部变量（包括Lambda所在类的this）。**
<br>

### （三）	&
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过**引用传递**的方式使用到定义Lambda为止时Lambda所在作用范围内可见的局部变量（包括Lambda所在类的this）。
<br>

### （四）	this
&nbsp;  &nbsp;  &nbsp;  &nbsp; Lambda函数体可以使用Lambda所在类中的成员变量。
<br>

### （五）	a
&nbsp;  &nbsp;  &nbsp;  &nbsp; **将a按值传递的方式使用**，**不能修改传递进来的a的拷贝**，要修改，需要添加mutable修饰符。
<br>
### （六）	&a
&nbsp;  &nbsp;  &nbsp;  &nbsp; **将a按引用传递的方式使用。**
<br>

### （七）	a, &b
&nbsp;  &nbsp;  &nbsp;  &nbsp; a按值传递，b按引用传递。
<br>

### （八）	=,&a, &b
&nbsp;  &nbsp;  &nbsp;  &nbsp; 除了a,b用引用传递，其他都是值传递。
<br>

### （九）	&，a,b
&nbsp;  &nbsp;  &nbsp;  &nbsp; 除了a,b用值传递，其他都是引用传递。
<br>

## 三．操作符重载函数参数
&nbsp;  &nbsp;  &nbsp;  &nbsp; **标识重载的()操作符的参数，没有参数时，这部分可以省略**。

&nbsp;  &nbsp;  &nbsp;  &nbsp;参数可以通过按值（如：(a,b)）和按引用（如：(&a,&b)）两种方式进行传递。
<br>

## 四．可修改标识符
&nbsp;  &nbsp;  &nbsp;  &nbsp; **mutable修饰符，加上后可以修改值传递进来的拷贝变量。**
<br>

## 五．函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp; 标识出函数返回值的类型，如：

&nbsp;  &nbsp;  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp;  &nbsp; ->int
<br>

## 六．函数体
&nbsp;  &nbsp;  &nbsp;  &nbsp; **即使没有实现也不能省略**






