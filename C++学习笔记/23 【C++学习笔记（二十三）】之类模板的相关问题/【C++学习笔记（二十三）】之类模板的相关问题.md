## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	类模板

**写法：**

```cpp
template<class NameType, class AgeType>
class Person
{
public:
	Person(NameType name, AgeType age)
	{
		this-> m_Name = name;
		this->m_Age = age;
	}
	NameType m_Name;
	AgeType m_Age;
}
```

<br>

**类模板不支持自动类型推导，需要显式指定类型：**


```cpp
Person<string,int> p(“大哥”, 100);
```


<br>

**类模板的类型可以有默认参数，但是函数模板没有默认参数**

```cpp
template<class NameType, class AgeType=int>
class Person{}
```


## 二.	当类模板作为函数的参数
<br>

类模板作为函数的参数有以下几种传参的形式：

**1.	指定传入类型**

&nbsp;  &nbsp;  &nbsp;  &nbsp;指定要传入的类模板的类型，如下所示：

```cpp
void doWork(Person<string, int> & p ) {}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;在传入的类模板中指定了类模板的参数类型。

**2.	函数模板化**

**&nbsp;  &nbsp;  &nbsp;  &nbsp;把类模板作为函数的参数，并且用函数模板来实现，类模板的参数类型作为函数模板的参数，函数模板可以自动推导参数的类型。**

```cpp
template<class T1, class T2>
void doWork(Person<T1, T2> & p){}
```

**3.	将类模板整体作为一个函数参数类型**

&nbsp;  &nbsp;  &nbsp;  &nbsp;将整个类模板都作为函数模板的一个参数类型。

```cpp
template<class T>
void doWork(T & p) {}
```

**代码如下：**

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;

//类模板
template<class NameType, class AgeType>
class Person
{
public:
	Person(NameType name, AgeType age)
	{
		this->m_Name = name;
		this->m_Age = age;
	}

	void PrintPerson()
	{
		cout << "Name: " << this->m_Name << "Age: " << this->m_Age << endl;
	}

public:
	NameType m_Name;
	AgeType m_Age;
};

//类模板作为函数参数

//1.指定模板类型
void DoBus1(Person<string, int>& p)
{
	p.m_Age += 20;
	p.m_Name += " vip";
	p.PrintPerson();
}

//2.类模板类型作为函数模板类型
template<class T1, class T2>
void DoBus2(Person<T1,T2>& p)
{
	p.m_Age += 30;
	p.m_Name += " vip";
	p.PrintPerson();
	
}

//3.类模板整体作为函数模板的参数类型
template<class T >
void DoBus3(T& p)
{
	p.m_Age += 40;
	p.m_Name += " vip";
	p.PrintPerson();
}

void test()
{
	Person<string, int> p("jack", 10);
	DoBus2(p);
}
int main()
{
	test();

	return 0;
}
```

<br>

## 三． 继承类模板的问题
**&nbsp;  &nbsp;  &nbsp;  &nbsp;当子类继承一个类模板时，我们必须要为类模板指定参数类型，不然类模板无法为参数类型分配指定的内存空间。因为，如果不指定参数类型，那么类模板不知道应该分配多少内存给参数。**
<br>

**1.	在子类中直接指定类型**

```cpp
template<class T>
class Base{}

class Child : public Base<int>{}
```

**&nbsp;  &nbsp;  &nbsp;  &nbsp;子类后面指定了<int>类型，那么基类模板类就知道参数类型T为int。**

**2.	子类同样为类模板，无须直接指定参数类型**

```cpp
template<class T>
class Base{}

template<class T1, class T2>
class Child2 : public Base<T2>
{}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，子类为类模板，T1为子类的参数类型，T2为基类类模板的参数类型。这两个参数类型都可以在用户调用时指定。
<br>

## 四．类模板类外实现成员函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;如何在类模板的类外实现成员函数呢？
**&nbsp;  &nbsp;  &nbsp;  &nbsp;与普通类的类外实现成员函数一样，我们需要添加类的作用域。但不同的是，类模板的作用域需要通过类模板的定义来实现。**

```cpp
template<class T1, class T2>
void Person<T1,T2>::showPerson(){}
```

## 五． 类模板的分文件编写问题

&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们将类模板分别写到 Person.h文件和Person.cpp文件中，Person.h只是声明，Person.cpp实现类模板，最终实现会出现错误。

&nbsp;  &nbsp;  &nbsp;  &nbsp;因为，C++是**单元编译**的，所谓单元编译是对每一个文件分别编译的，因此分别对Person.cpp编译，对Person.h编译。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;语法当然是没有问题的，但是在Person.h中，类模板的成员函数一开始是不会创建的，因为确定不了具体的类型参数。因此，即使在类模板实现的cpp文件中引入了Person.h头文件，但是Person.cpp中成员函数的代码并没有生成，也就无法链接到Person.cpp的成员函数代码。**

**如何解决：**

**&nbsp;  &nbsp;  &nbsp;  &nbsp;建议类模板不要分文件编写。将Person.cpp中的代码都写到Person.h中，即类模板在类内声明，在类外实现。同时，将Person.h的后缀名改为Person.hpp。.hpp一般用于写模板所用。**

**Person.hpp代码：**

```cpp
#pragma once
#include<iostream>
using namespace std;

template<class T1, class T2>
class Person
{
public:
	Person(T1, T2);

	void showPerson();

	T1 m_name;
	T2 m_age;
};

template<class T1, class T2>
Person<T1, T2>::Person(T1 name, T2 age)
{
	this->m_name = name;
	this->m_age = age;
}

template<class T1, class T2>
inline void Person<T1, T2>::showPerson()
{
	cout << "姓名： " << this->m_name << "年龄： " << this->m_age << endl;
}
```

## 六. 类模板的友元函数
<br>

**1.直接在类模板内声明并定义友元函数**

**&nbsp;  &nbsp;  &nbsp;  &nbsp;在类模板内直接声明定义友元函数，则该友元函数默认为全局函数，可以直接调用。**

```cpp
template<class T1,class T2>
class Person
{
	friend void printPerson( Person<T1,T2> &p) {}
}
```
<br>

**2.友元函数类模板外实现**

&nbsp;  &nbsp;  &nbsp;  &nbsp;友元函数在类模板内声明，在类模板外实现，需要的步骤比较多。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;如果我们在类模板内声明了友元函数如下所示：**

```cpp
template<class T1,class T2>
class Person
{
	friend void printPerson( Person<T1,T2> &p) {}
}
```

**&nbsp;  &nbsp;  &nbsp;  &nbsp;而在类模板外定义了友元函数，由于T1，T2在类模板外是未定义的，因此需要加上模板**。如下所示：

```cpp
template<class T1, class T2>
void printPerson(Person<T1, T2> &p) {}
```

**&nbsp;  &nbsp;  &nbsp;  &nbsp;但是可以很明显地看出，类模板内声明地友元函数是普通函数，而类外实现地友元函数是一个模板函数，这明显是不匹配的。**

**因此，以下是真正的解决步骤：**

**1.	友元函数声明为模板函数**

**&nbsp;  &nbsp;  &nbsp;  &nbsp;我们需要为类模板内声明的友元函数添加<>**，如下所示：

```cpp
friend void printPerson<>( Person<T1,T2> &p) {}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这样，就告诉了编译器这是模板函数的声明，这样两个函数就匹配了。

**2.提前声明模板函数，类模板**

**&nbsp;  &nbsp;  &nbsp;  &nbsp;但是，这样还是不行，由于在类模板外实现模板函数，而友元函数声明是在类模板的内部，因此，我们需要在类外提前声明该模板函数，同时，该模板函数声明中的Person类模板也是未知的，也需要提前声明。**

```cpp
template<class T1, class T2> class Person;
template<class T1, class T2> void printPerson(Person T1, Person T2)
```
<br>


## 七. 类模板封装小型数组
**<br>代码如下：**

```cpp
#pragma once
#include<iostream>
using namespace std;

template<class T>
class MyArray
{
public:
	//构造
	explicit MyArray(int capacity)		//防止隐式类型转换
	{
		this->m_Capacity = capacity;
		this->m_Size = 0;
		this->pAddress = new T[this->m_Capacity];	//开辟堆区空间
	}
	
	//拷贝构造
	MyArray(const MyArray& array)
	{
		this->m_Capacity = array.m_Capacity;
		this->m_Size = array.m_Size;
		this->pAddress = new T[this->m_Capacity];
		//获取拷贝数据
		for (int i = 0; i < this->m_Size; ++i)
		{
			this->pAddress[i] = array[i];
		}
	}

	//析构
	~MyArray()
	{
		//堆空间释放
		if (this->pAddress != NULL)
		{
			delete[]this->pAddress;
			this->pAddress = NULL;
		}
	}

	// =重载
	MyArray& operator=(MyArray& array)
	{
		//判断原始数据是否存在，如果存在就清空
		if (this->pAddress != NULL)
		{
			delete[]this->pAddress;
			this->pAddress = NULL;
		}

		this->m_Capacity = array.m_Capacity;
		this->m_Size = array.m_Size;
		this->pAddress = new T[this->m_Capacity];
		for (int i = 0; i < this->m_Size; ++i)
		{
			this->pAddress[i] = array[i];
		}
	}

	// []重载
	T& operator[](int index)
	{
		return this->pAddress[index];
	}

	//尾插法
	void push_back(T val)
	{
		this->pAddress[this->m_Size] = val;
		this->m_Size++;
	}

	//获取数组大小
	int getSize()
	{
		return this->m_Size;
	}

	//获取数组容量
	int getCap()
	{
		return this->m_Capacity;
	}

private:
	T* pAddress;	//指向堆区的指针
	int m_Capacity;	//容量
	int m_Size;		//大小
};
```






