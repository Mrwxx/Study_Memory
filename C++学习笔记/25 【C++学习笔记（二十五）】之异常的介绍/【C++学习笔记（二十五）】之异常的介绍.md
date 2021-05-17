## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	异常的基本思想：

&nbsp;  &nbsp;  &nbsp;  &nbsp;让一个函数在发现了自己无法处理的错误时抛出一个异常，然后它的（直接或者间接）调用者能够处理这个问题。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;将问题检测和问题处理相分离。 在异常处理过程中，由问题检测代码可以抛出一个对象给问题处理代码，通过这个对象的类型和内容，实际上完成了两个部分的通信，通信的内容是“出现了什么错误”。**
<br>

## 二.	异常的注意事项

**&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 抛出异常，必须要处理。如果不处理，就会中断程序。所以异常使得开发出来的程序更加健壮。**


&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 如果匹配的处理未找到，则运行函数terminate将自动被调用，其缺省功能调用abort终止程序。


&nbsp;  &nbsp;  &nbsp;  &nbsp;3. 如有多个异常对象，用多个对应的catch语句进行捕获。catch语句会根据异常对象出现的先后顺序进行检查。匹配的catch语句捕获并处理异常(或继续抛出异常)


**&nbsp;  &nbsp;  &nbsp;  &nbsp;4. 处理不了的异常，可以在catch的最后一个分支，使用throw，向上抛。**


&nbsp;  &nbsp;  &nbsp;  &nbsp;5. 异常处理时可以跳级处理，如果不想处理，只写throw，让上一层处理。总之异常最终一定要在某一层进行处理，不然，程序就会终止。


&nbsp;  &nbsp;  &nbsp;  &nbsp;6. catch(…)省略号代表所有其他类型
<br>

## 三.	处理异常语法：

&nbsp;  &nbsp;  &nbsp;  &nbsp;在try代码块中进行正常逻辑处理，并且在会出错的位置抛出一个异常对象，在try代码块之下catch捕获响应的异常对象，输出错误的原因。这样就避免了程序的终止，也便于后续的修改。

```cpp
int myDevide(int a, int b)
{
	if( b == 0)
		throw -1;	//抛出异常
	return a / b;
}

try
{
	myDevide(a, b);
}
catch(int)
{
	cout << “int类型异常” << endl;
}
```

## 四.	自定义异常类


&nbsp;  &nbsp;  &nbsp;  &nbsp;自己定义一个异常类，那么就可以将某一类异常整合到一个类中。

示例如下：

```cpp
class MyException
{
public:
	void printError()
	{
		cout << “自定义异常” << endl;;
	}
}

int myDevide(int a, int b)
{
	if( b == 0)
	{
		throw myException();	//匿名对象
	}
	return a / b;
}

catch(MyException e)
{
	e.printError();
}
```

## 五.	栈解旋

&nbsp;  &nbsp;  &nbsp;  &nbsp;所谓栈解旋，从进入try代码块起，到异常对象被抛出前，在这期间的创建的栈对象都会被自动地析构。析构的顺序与构造的顺序相反。

如下所示：

```cpp
try
{
		Person p1;
		Person p2;
		throw myException();
}
```



&nbsp;  &nbsp;  &nbsp;  &nbsp;P1，p2都是在栈上开辟的对象，因此在抛出myException()异常对象前，这两个栈对象就会被自动析构。


## 六．异常接口声明


&nbsp;  &nbsp;  &nbsp;  &nbsp;1.当我们在声明函数时，可以直接在参数框后面列出该函数可能抛出的异常类型：

```cpp
void func() throw(A, B) {}		//这个函数func只能抛出A，B类型异常及其子类型。
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;2.如果函数声明时没有包含异常接口声明，则此函数以抛任何类型的异常:

```cpp
void func() {}
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;3.如果函数声明包含了异常接口声明，但是没有写任何异常类型，说明该函数不能抛出任何类型异常：

```cpp
void func() throw() {}
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;4.如果函数抛出了它的异常接口没有声明的异常类型，那么unexpected函数会被调用，该函数默认调用terminate函数中断程序。

<br>

## 七．异常变量的生命周期

&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们抛出异常变量时，可能会抛出变量的引用，指针，对于每一种不同类型的异常变量，它的声明周期都是不同的。

如：

**&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	throw( MyException() )，catch(MyException e)。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;抛出异常时调用默认构造，那么捕获异常时，就会多调用一次拷贝构造函数和之后的析构函数。且默认构造和拷贝构造的析构都是在捕获异常之后。


**&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	throw( &MyException() ), catch(MyException*e)**

&nbsp;  &nbsp;  &nbsp;  &nbsp;抛出异常对象的指针，捕获异常对象的指针，因为该异常对象开辟在了栈空间上，那么该异常对象在捕获异常前就会被释放。


**&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	throw( new MyException() )，catch(MyException*e)**

&nbsp;  &nbsp;  &nbsp;  &nbsp;用new在堆区上开辟对象，抛出指针，则该异常对象不会被自动释放，而是由程序员自己释放。


**&nbsp;  &nbsp;  &nbsp;  &nbsp;4.	throw( MyException() ), catch(MyException &e)**

&nbsp;  &nbsp;  &nbsp;  &nbsp;捕获异常对象的引用，那么只需要抛出异常的默认构造，就不会调用拷贝构造了，提高速度。推荐使用引用的方式来捕获异常。


## 八. 异常的多态使用

&nbsp;  &nbsp;  &nbsp;  &nbsp;多态的思想我们之前已经介绍过了，便于功能的拓展。

&nbsp;  &nbsp;  &nbsp;  &nbsp;当异常与多态结合起来时，就可以将不同的异常整合成不同的子类，并继承于一个公共的基类，便于之后的拓展。通过一个接口printError可以调用不同的异常提示，抛出不同的错误对象。

如下所示：

```cpp
class BaseException
{
public:
	virtual void printError(){}
}

class NullPointerException : public BaseException
{
public:
	virtual void printError()
	{
		cout << “空指针异常” << endl;
	}
}

void test()
{
	try
	{
		throw NullPointerException();	//匿名对象
	}
	catch(BaseException & e)		//基类的指针或引用指向子类
	{
		e.printError();				//调用子类的函数
	}
}
```

## 九．系统标准异常

&nbsp;  &nbsp;  &nbsp;  &nbsp;如何使用系统自带的异常？系统标准异常也是从exception异常基类开始继承的，异常类继承层级结构图如下：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720194954446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

1.	 在上述继承体系中，每个类都有提供了构造函数、拷贝构造函数、和赋值操作符重载。
2.  logic_error类及其子类、runtime_error类及其子类，它们的构造函数是接受一个string类型的形式参数，用于异常信息的描述
3.  所有的异常类都有一个what()方法，返回const char* 类型（C风格字符串）的值，描述异常信息。

**注意：要调用系统标准异常**，就要

```cpp
#include<stdexcept>
```

其他的与自定义的异常使用方法一样。










