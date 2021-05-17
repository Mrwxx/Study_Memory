## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.单例模式定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;单例模式是一种常用的软件设计模式。**在它的核心结构中只包含一个被称为单例的特殊类**。通过单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。

## 二.如何设计一个单例模式的类
### （一）联想
&nbsp;  &nbsp;  &nbsp;  &nbsp;**由单例模式的定义我们很容易联想到静态变量**，因为静态变量也是只有一个，因此，我们就需要用静态成员变量与静态成员函数来设计一个单例模式的类。
### （二）设计
&nbsp;  &nbsp;  &nbsp;  &nbsp;单例模式要做的就是仅仅创建一个类对象，且只能有这一个类对象。如何做到？
&nbsp;  &nbsp;  &nbsp;  &nbsp;**1. 将默认构造函数和拷贝构造函数设置为private,因为不能随意调用构造函数创建类对象。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;**2. 再设置private里的static成员变量为类指针指向类对象，在类外进行定义这个static成员变量，即定义一个独立存在的类对象。因为单例模式只能有一个类对象，因此用static成员变量来访问，由于这个类对象是由static成员变量定义的，因此它在编译阶段就已经形成了。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;**3,. 同时，也要为这个类对象设置访问私有数据的接口，注意，通过static成员函数getInstance来获取static成员变量，因为如果用普通成员函数，则必须用类对象来调用，而类对象又是由static成员变量指针指向的，无法分清普通成员函数的地址，因此还是得用static声明成员函数**。

## 三. 总结
&nbsp;  &nbsp;  &nbsp;  &nbsp;其实总的来看就是，要创建单例模式的类，就要将它的构造函数都设成private，不能用普通创建类对象的方式创建多余的类对象，同时设置static成员变量指向唯一的类对象，设置static成员函数接口getInstance，只对类开放，这样就没有创建多余类对象的风险，private成员变量也不会被修改。

## 四. 实例代码
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个打印机单例模式代码如下：

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;

//打印机类
class Printer
{
//私有化构造函数
private:
	Printer()
	{
		m_count = 0;
	}
	Printer(const Printer&);
//函数调用接口
public:
	//获取类指针
	static Printer* getInstance()
	{
		return singlePrinter;
	}

	void printText(string text)
	{
		cout << text << endl;
		m_count++;
		cout << "打印机使用了" << m_count << "次" << endl;
	}

private:
	static Printer* singlePrinter;	// static成员变量声明
	int m_count;		//打印次数
};

Printer* Printer::singlePrinter = new Printer;	//定义static成员变量

void test01()
{
	//通过函数接口获取打印机对象指针
	Printer* printer = Printer::getInstance();
	printer->printText("报告1");
	printer->printText("报告2");
}

int main()
{
	test01();
	
	return 0;
}
```


 **&nbsp;  &nbsp;  &nbsp;  &nbsp; 以上就是初学单例模式对于单例模式的理解，记录下来，有错误的地方大家轻喷！**

