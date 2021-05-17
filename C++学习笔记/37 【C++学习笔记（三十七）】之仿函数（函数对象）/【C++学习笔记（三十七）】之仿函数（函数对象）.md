## 一．仿函数（函数对象）
&nbsp;  &nbsp;  &nbsp;  &nbsp;**重载了函数调用操作符“（）“的类，其对象称为函数对象**，因为该类能够像函数一样调用，因此也叫仿函数。
<br>

## 二．分类：
<br>
&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 一元仿函数  重载operator()函数中有一个参数

<br>


&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 二元仿函数  重载operator()函数中有两个参数
<br>

## 三．仿函数调用方式：
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 可以用**函数调用**的方式调用	MyPrint(100)

&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 也可以用**匿名对象**的方式调用	MyPrint()(100);
<br>

## 四．总结
**&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 函数对象超过函数，内部保存状态，因为仿函数是一个类，类中的成员变量都可以保存该类的状态。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 函数对象通常不会构造函数和析构函数，所以在构造和析构时不会发生任何问题，避免了函数调用的运行时问题。

&nbsp;  &nbsp;  &nbsp;  &nbsp;3. 可内联编译，性能好

**&nbsp;  &nbsp;  &nbsp;  &nbsp;4. 可以作为类型，与模板配合使用（参照之前set容器使用仿函数自定义排序规则）**
<br>

## 五．代码

```cpp
class MyPrint
{
public：
	void operator() (int num)
	{
		cout << num << endl;
	}
}

void test()
{
	MyPrint myPrint;
	myPrint(20);
}

int main()
[
	test();
	return 0;
}
```

