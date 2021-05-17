## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. C++的类中成员变量与成员函数的存储
 **&nbsp;  &nbsp;  &nbsp;  &nbsp; 在C++的类中，虽然将成员变量与成员函数风筝在了一起，但是他们是分开存储的。**
1. C++的非静态成员变量是属于类对象的，占类对象的空间，
2. 成员函数却不属于类对象，不占类对象的存储空间

## 二. 总结
1. 非静态成员变量，属于类对象，占用类对象空间
2. 非静态成员函数  不属于类对象，不占用类对象空间
3. 静态成员函数，静态成员变量都不属于类对象，都是单独的空间存储，

## 三. 验证

 **&nbsp;  &nbsp;  &nbsp;  &nbsp; 空类的大小为1个字节，因为每个实例的类对象，都有唯一的地址，每个类对象中有一个char 类型来维护这个地址。**
 **&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过查看类的字节大小来验证**
 

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

class Person
{
public:
	int a;			//非静态成员变量，属于类对象
	void func( ) {};	//非静态成员函数 不属于类对象
	static int b;		//静态成员变量 ，不属于类对象
	static void  func2(){}; //静态成员函数 ，不属于类对象
	
};

void test()
{
	cout  << sizeof(Person) << endl;

}

int main(){

	test();

	return 0;
}
```


