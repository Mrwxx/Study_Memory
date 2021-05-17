## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

### new 运算符的使用
#### 一. new的简单介绍：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;new运算符用于在**堆区开辟空间**，所有new出来的对象，对**返回该类对象的指针**。
#### 二. new 与 malloc 的异同点：
1. malloc作用相同， **malloc 也是在堆区开辟空间**， 但返回的是 **void*** ，需要强制转换。
2. malloc 不会调用构造函数， new 会调用构造函数。
3. **malloc 配合 free** 释放堆区空间  ，**new 配合 delete**释放堆区空间。


#### 三. new 构造数组

 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过 **new 开辟数组**一定会**调用默认构造函数**，所以一定要提供默认构造函数
#### 四. delete [] 释放数组空间

 &nbsp;  &nbsp;  &nbsp;  &nbsp;单一对象与数组的内存构造是不同的，**数组的内存中还包括“数组大小记录”**，使得**delele时知道应该调用几次析构函数**。当我们使用delete时，必须让delete知道指针所指向的内存空间是否存在一个“数组大小记录”，因此，使用**delete[]**就能让delete知道这是一个数组，从而清楚应该调用几次析构函数。


 &nbsp;  &nbsp;  &nbsp;  &nbsp;**注意**：当用 void* 接受 new 出来的指针，会出现内存释放的问题， 无法释放内存

#### 代码如下，有详细注释：

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

class Person
{
public:
	Person()
	{
		cout << "默认构造函数调用" << endl;
	}

	Person(int a)
	{
		cout << "有参构造函数调用" << endl;
	}

	~Person()
	{
		cout << "析构函数调用" << endl;
	}
};

void test01()
{
	Person p1;		//在栈区开辟空间
	Person* p2 = new Person;	//在堆区开辟空间

	//所有new出来的对象，对返回该类型对象的指针
	// malloc 也是在堆区开辟空间， 但返回的是 void* ，需要强制转换
	// malloc 不会调用构造函数， new 会调用构造函数
	// malloc 配合 free 释放堆区空间  new 配合 delete释放堆区空间
	delete p2;

}

void test02()
{
	void* p = new Person(10);
	//当用 void* 接受 new 出来的指针，会出现内存释放的问题， 无法释放内存
	delete p;	//虽然使用了delete,但是没有调用析构函数，没有释放内存
}

void test03()
{
	// 通过 new 开辟数组一定会调用默认构造函数，所以一定要提供默认构造函数
	Person* pArray = new Person[10];
	//Person pArray2 ={Person(1), Person(2)};	在栈上开辟数组空间，可以用有参构造

	//释放数组 delete[]
	delete[] pArray;

}

int main()
{
	test03();
	return 0;
}
```


