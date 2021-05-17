## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	适配器

&nbsp;  &nbsp;  &nbsp;  &nbsp;我们都知道，笔记本电脑的转接头适配器能够扩展缺少的接口，STL中的适配器也是一样的效果，**它能够将某些类型扩展接口，适配到其他类型中**。即扩展参数的个数，如将一元函数扩展为二元函数…

&nbsp;  &nbsp;  &nbsp;  &nbsp;**需要引入头文件**

```cpp
#include<functional>
```
<br>

## 二．仿函数 适配器扩展参数到二元
### （一）应用场景：
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;本为一元仿函数MyPoint()，需要将用户输入的num适配到原有的MyPoint()仿函数中，因此用适配器将MyPoint()仿函数适配为二元仿函数。
<br>

### （二）步骤
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;**第一步**： **绑定数据 bind2nd**，意思是绑定数据num到第二个参数start上; **绑定数据bind1st**，将数据num绑定到第一个参数v上

&nbsp;  &nbsp;  &nbsp;  &nbsp;**第二步**：**仿函数的类继承 binary_function<参数类型1， 参数类型2，返回值类型>**

&nbsp;  &nbsp;  &nbsp;  &nbsp;**第三步**：加const限定 operator()，不允许修改

```cpp
class MyPoint : public binary_function<int, int, void>
{
public:
	void operator() (int v, int start) const
	{
		cout << v + start << endl;
	}
}	

void test()
{
	vector<int> v;
	for(int i = 0 ; i < 10; ++i)
	{	
		v.push_back(i);
	}
	int num;
	cin >> num;
	for_each(v.begin(), v.end(), bind2nd(MyPoint(), num) );
}
```
<br>

## 三．取反适配器 
&nbsp;  &nbsp;  &nbsp;  &nbsp;not1对一元函数对象取反，not2对二元函数对象取反
<br>

### （一）步骤
&nbsp;  &nbsp;  &nbsp;  &nbsp;继承 unary_function<参数类型1，返回值类型>，限制const
<br>

### （二）场景：
&nbsp;  &nbsp;  &nbsp;  &nbsp;在vector中寻找大于5的第一个数字，后更改需求，寻找小于5的第一个数字，将该一元仿函数转换成相反的功能。not1适配器，实现相反功能
<br>


```cpp
class GreaterThanFive : public unary_function<int, bool>
{
public:
	bool operator() (int v)	const
	{
		return v > 5;
	}
}

void test()
{
	vector<int> v;
	for(int i = 0; i < 10; ++i)
	{
		v.push_back(i);
	}

	vector<int> iterator pos = find_if(v.begin(), v.end(), not1(GreaterThanFive()) );
	if( pos != v.end())
	{
		cout << “找到小于5的数字为：” << *pos << endl;
	}
	else
	{
		cout <<”未找到” << endl;
	}
}
```

<br>

## 四．普通函数指针适配器 ptr_fun

&nbsp;  &nbsp;  &nbsp;  &nbsp;普通函数指针不像前面说的仿函数是个类能够继承，**我们需要将普通函数指针适配为函数对象（仿函数），用ptr_fun(普通函数指针)适配成了函数对象**，也能够向前面一样扩展接口参数。但不需要继承，以及限制const
<br>


```cpp
void MyPrint(int v, int start)
{
	cout << v +start <<endl;
}

void test()
{
	vector<int> v;
	for(int i = 0; i < 10; ++i)
	{
		v.push_back(i);
	}
	int num;
	cin >> num;
	for_each(v.begin(), v.end(), bind2nd(ptr_fun(MyPoint), num ) );
}
```
<br>

## 五．成员函数适配器 mem_fun_ref / mem_fun
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;针对类中的内容，我们之前对类中的内容进行排序时，设置一个全局回调函数，调用回调函数。因为是对同一个类的内容进行处理，那么用类中的成员函数不是更好么？

**&nbsp;  &nbsp;  &nbsp;  &nbsp;因此，可以用成员函数适配器mem_fun_ref将类中的成员函数适配成回调函数。**

**两种形式：**

&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 当vector<Person> v; vector中存储的是**Person对象**时，用**mem_fun_ref适配器**。

```cpp
 mem_fun_ref( &Person::showPerson)`	
```

 
&nbsp;  &nbsp;  &nbsp;  &nbsp;将Person类中的showPerson()成员函数配置成回调函数,注意括号内为Person类的showPerson的成员函数的地址。

&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 当vector<Person *> v; vector中存储的是**Person的对象指针**时，使用**mem_fun适配器**。


```cpp
mem_fun(&Person::showPerson));
```
<br>


```cpp
class Person
{
public:
	Person(string name, int age)
	{
		this->m_Name = name;
		This->m_Age = age;
	}

	void showPerson()
	{
		cout << “姓名：” << m_Name <<” 年龄：“ << m_Age <<endl;
	}

	string m_Name;
	int m_Age;
}

void test()
{
	vector<Person> v;
	Person p1(“1”, 1);
	Person p2(“2”, 2);
	v.push_back(p1);
	v.push_back(p2);

	for_each(v.begin(), v.end(), mem_fun_ref(&Person::showPerson));
}
```


<br>

**以上就是STL中适配器的简单介绍，初次学习，请多关照！！！**

