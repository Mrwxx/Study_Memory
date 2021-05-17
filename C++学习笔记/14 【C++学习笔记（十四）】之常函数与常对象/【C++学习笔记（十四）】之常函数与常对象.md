## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 常函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;**我们都知道this指针是指向类对象的, 且这个地址是一直不变的，即const性质的。设置一个类Person，可以将它记为 Person * const this;** 字面意思就是this这个指针的值是不变的，即所指向的类对象是不变的。
&nbsp;  &nbsp;  &nbsp;  &nbsp;那么如果要让指针指向的类对象中的值无法修改时，即	Const Person* const this;要怎么做呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp;这就要用到**常函数**了，如

```cpp
void c1() const{}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;**可以看到，我们在普通的函数后面增加了const ，代表着这个函数是常函数。在这个函数中，this指针指向的类对象的值是无法修改的 。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;但同时，C++也给我们放宽了一点，提供了**mutable关键字来突破const的限制**，若类中有些值我们需要修改，便可以声明为mutable，即使是在常函数中，也是可以修改的。

```cpp
mutable int a;
```

## 二. 常对象

&nbsp;  &nbsp;  &nbsp;  &nbsp;常函数只能在函数的范围内控制类对象不能被修改，如果我想把这个控制范围扩大呢？扩大到整个类对象的范围，就变成了**常对象**了。**即定义一个整个类对象中的值都是无法被修改的。**

```cpp
const Person p2;
```

**&nbsp;  &nbsp;  &nbsp;  &nbsp;常对象不可以调用普通的成员函数，因为普通的成员函数有修改成员变量的风险，而常变量已经声明了不允许修改对象的属性。因此，常对象只能调用常函数。**  
**&nbsp;  &nbsp;  &nbsp;  &nbsp;常对象调用常函数：**

```cpp
Void c2() const{}
Const Person p2;
P2.c2();
```

## 三. 代码：

```cpp
#include<iostream>
using namespace std;

class Person {
public:
	Person()
	{
		a = 0;
		b = 0;
	}

	void text() const	//常函数
	{
		cout << "a = " << a << endl;
		cout << "b = " << b << endl;
	}

	int a;
	int b;
};

void test()
{
	Person p1;
	p1.text();

	const Person p2;		//常对象
	p2.text();
}

int main()
{
	test();
	return 0;
}
```


