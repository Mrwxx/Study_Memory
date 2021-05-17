## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 浅拷贝与深拷贝

 &nbsp;  &nbsp;  &nbsp;  &nbsp;我们通过**代码**来解释什么是浅拷贝，什么是深拷贝。
1. 首先，写一个**类Person**，设置无参构造函数，有参构造函数，析构函数，姓名char*m_Name，年龄int m_age ，编译器自动提供拷贝构造函数。
2. 有参构造函数中传入参数(const char* name，int age)，有参构造函数在设置const char* 时需要**开辟堆空间**来存储。因此，**析构函数**中需要将开辟的堆空间给**free掉**。
3. 当主调函数中设置了拷贝构造函数时，生成的p2与p1是相同的，两个类对象中所存储的内容是一样的，第一个属性是char *，指针类型，指向某个地址，第二个属性是int,存储的是个数。两个类对象都指向相同的地址。
4. **当析构函数free掉p1所指向的地址开辟的堆空间后，p2就找不到对应的地址了，p2的析构函数就会因此崩掉。这就是浅拷贝，仅仅是简单地复制。**
5. 要解决这个问题，就是**深拷贝**的方法。自己提供拷贝构造函数，设置拷贝构造函数不仅仅是简单地拷贝值，而是为新的**拷贝开辟新的堆空间**，这样两个类对象在析构函数free时就不会有错误了。

代码如下：

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>	
using namespace std;

class Person
{
public:
	//构造函数
	Person() {}
	//有参构造函数
	Person(const char* name, int age)
	{
		//姓名需要开辟堆空间
		m_Name = (char*)malloc(strlen(name)+1);
		strcpy(m_Name, name);
		m_age = age;
	}
	//拷贝构造函数  系统提供默认拷贝构造，值拷贝
	//深拷贝，自己提供拷贝构造函数。因为浅拷贝导致析构函数两次free
	Person(const Person& p)
	{
		m_age = p.m_age;
		//开辟新的堆空间,
		m_Name = (char*)malloc(strlen(p.m_Name) + 1);
		strcpy(m_Name, p.m_Name);
	}
	//析构函数
	~Person()
	{
		cout << "析构函数调用" << endl;
		//有参构造函数开辟了堆空间，需要free
		if (m_Name != NULL)
		{
			free(m_Name);
			m_Name = NULL;
		}
	}
	//姓名
	char* m_Name;
	//年龄
	int m_age;

};

void test01()
{
	Person p1("lol", 10);
	Person p2(p1);		//调用了拷贝构造
}

int main()
{
	test01();

	return 0;
}
```


