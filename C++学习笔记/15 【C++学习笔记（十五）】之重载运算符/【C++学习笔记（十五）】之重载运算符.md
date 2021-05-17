## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 运算符重载
### （一）定义：
&nbsp;  &nbsp;  &nbsp;  &nbsp;对已有的运算符进行重新的定义，以适应不同的数据类型。其实还是一个函数，只不过函数名改为了operator加上要重载的运算符。

### （二）参数：
&nbsp;  &nbsp;  &nbsp;  &nbsp;运算符重载函数的参数个数取决于：

**&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	运算符是一元运算符还是二元运算符
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	运算符重载函数是全局函数还是成员函数。对于全局函数，一元运算符有一个参数，二元运算符有两个参数；对于成员函数，一元运算符没有参数，二元运算符有一个参数。**

## 二.运算符重载实例
### （一）+号运算符重载代码：

```cpp
#define _CRT_SECURE_NO_WARNINIGS
#include<iostream>
using namespace std;

class Person
{
public:
	Person() {};
	Person(int a, int b) : m_A(a), m_B(b)
	{}

	//+号重载 成员函数,将成员变量对应相加
	Person operator+(Person& p)
	{
		Person tmp;
		tmp.m_A = this->m_A + p.m_A;
		tmp.m_B = this->m_B + p.m_B;
		return tmp;

	}

	int m_A;
	int m_B;
};

//全局函数，重载
//Person operator+ (Person & p1, Person & p2)
//{
//	Person tmp;
//	tmp.m_A = p1.m_A + p2.m_A;
//	tmp.m_B = p1.m_B + p2.m_B;
//	return tmp;
//}

void test()
{
	Person p1(10, 10);
	Person p2(20, 20);

	Person p3 = p1 + p2;		//成员函数，全局函数都可以被调用
	cout << "p3.m_A = " << p3.m_A << endl;
	cout << "p3.m_B = " << p3.m_B << endl;

	
}

int main()
{
	test();
	return 0;
}
```

### （二）左移运算符重载

&nbsp;  &nbsp;  &nbsp;  &nbsp;首先，我们想要实现的需求是用左移运算符<<直接输出输出类，实现如下所示：
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;cout << p
&nbsp;  &nbsp;  &nbsp;  &nbsp;输出为
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;P的m_A为… p的m_B为…

**&nbsp;  &nbsp;  &nbsp;  &nbsp;要实现这种功能，就要对cout进行重载。之前说到，重载运算符分为成员函数重载和全局函数重载，但是左移运算符<<前面已经有了cout，因此成员函数重载是无法使用的，只能用全局函数重载。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;同时，左移运算符还有连续输出的功能，这一点就要通过重载的全局函数返回cout引用来实现。
&nbsp;  &nbsp;  &nbsp;  &nbsp;当需要输出的数据为类中的私有数据时，还需要将重载全局函数添加为类的友元函数。

代码如下：

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

class Person
{
	friend ostream& operator<<(ostream& cout, Person& p1);

public:
	Person(){}
	Person(int a, int b)
	{
		this->m_A = a;
		this->m_B = b;
	}

private:
	int m_A;
	int m_B;
};

//全局函数重载
ostream& operator<<(ostream& cout, Person& p1)
{
	cout << "m_A = " << p1.m_A << "m_B = " << p1.m_B;
	return cout;
}

void test()
{
	Person p1(10, 10);
	cout << p1 << endl;
}

int main()
{
	test();

	return 0;
}
```

### （三）前置后置++重载

&nbsp;  &nbsp;  &nbsp;  &nbsp;如何区分前置++和后置++的重载函数？
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;用占位参数区分：

&nbsp;  &nbsp;  &nbsp;  &nbsp;前置++： MyInterger& operator++(){}
&nbsp;  &nbsp;  &nbsp;  &nbsp;后置++： MyInterger operator++(int){}

**&nbsp;  &nbsp;  &nbsp;  &nbsp;前置++ 返回的是类的引用，因为前置++，数据值已经+1了，返回的是已经+1的类，可以用引用，一遍后续操作。
&nbsp;  &nbsp;  &nbsp;  &nbsp;后置++ 返回的是类，因为需要开辟临时变量存储未+1的值，返回的是临时变量，是不能用引用的。**
代码：

```cpp
#include<iostream>
Using namespace std;
class MyInteger
{
	friend ostream& operator<<(ostream& cout, MyInteger);

public:
	MyInteger()
	{
		m_Num = 0;
	}

	//前置++重载
	MyInteger& oeprator++()
	{
		this->m_Num++;
		return *this;
	}

	//后置++重载
	MyInteger operator++(int)
	{
		//临时变量保存
		MyInteger tmp = *this;
		m_Num++;
		return tmp;
	}

	int m_Num;
};

ostream& operator<<(ostream& cout, MyInteger& myInt)
{
	cout << myInt.m_Num;
	return cout;
}
```


