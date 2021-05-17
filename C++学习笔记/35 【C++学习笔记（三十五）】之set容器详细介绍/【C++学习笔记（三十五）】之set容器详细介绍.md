## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．set容器

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 之间已经讲过，set容器与map容器都是关联型容器，set容器不像map容器那样，有键值和对应的实值，set容器的元素的键值就是实值。因此set容器是不允许有相同的键值的。**

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于set容器的键值就是对应的实值，且关联型容器在数据插入时已经将键值和实值绑定了，因此，**不能随意更改set的元素值**，修改set的元素值相当于修改键值，这会破坏set的排序结构。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 因此，无法通过set的迭代器修改set元素值，即set的iterator是const_iterator
<br>
## 二．multiset容器
<br>
&nbsp;  &nbsp;  &nbsp;  &nbsp; multiset特性和用法和set完全一样，差别在于multiset允许键值重复。

<br>

## 三．set容器常用操作
### （一）	set构造函数

```cpp
set<T> st;//set默认构造函数：
mulitset<T> mst; //multiset默认构造函数: 
set(const set &st);//拷贝构造函数
```
<br>

### （二）	set赋值操作
<br>

```cpp
set& operator=(const set &st);//重载等号操作符
swap(st);//交换两个集合容器
```
<br>

### （三）	set大小操作
<br>


```cpp
set& operator=(const set &st);//重载等号操作符
swap(st);//交换两个集合容器
```

<br>

### （四）	set插入和删除操作
<br>

```cpp
insert(elem);//在容器中插入元素。
clear();//清除所有元素
erase(pos);//删除pos迭代器所指的元素，返回下一个元素的迭代器。
erase(beg, end);//删除区间[beg,end)的所有元素 ，返回下一个元素的迭代器。
erase(elem);//删除容器中值为elem的元素。
```
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp; insert返回 pair<set<>::iterator,bool>，**bool意味着是否插入成功**

```cpp
pair<set<int>::iterator, bool> ret = s1.insert(10);
```
<br>

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 对组(pair)将一对值组合成一个值，这一对值可以具有不同的数据类型，两个值可以分别用pair的两个公有属性first和second访问。**
<br>

```cpp
pair.first
pair.second
```
<br>


&nbsp;  &nbsp;  &nbsp;  &nbsp; 如何**构造pair**呢？两种方法：

第一种：

```cpp
pair<string, int> p(string(“Tom”), 100);
```

第二种：

```cpp
pair<string, int> p2 = make_pair(“Jerry”, 200);
```

<br>

### （五）	set查找操作
<br>

```cpp
find(key);//查找键key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
count(key);//查找键key的元素个数
lower_bound(keyElem);//返回第一个key>=keyElem元素的迭代器。
upper_bound(keyElem);//返回第一个key>keyElem元素的迭代器。
equal_range(keyElem);//返回容器中key与keyElem相等的上下限的两个迭代器。
```
<br>


### （六）	set的排序规则
&nbsp;  &nbsp;  &nbsp;  &nbsp; set容器的元素在插入后就已经和键值绑定了，顺序是无法修改的，因此要想修改set的排序规则，**就必须在元素插入前修改。即set容器初始化时可以设置排序规则。**

**&nbsp;  &nbsp;  &nbsp;  &nbsp; set容器在初始化set时，第二个模板参数可以设置排序规则**，如下所示：

```cpp
set<int, myCompare> s1;
```

**&nbsp;  &nbsp;  &nbsp;  &nbsp; myCompare是一个仿函数**，该仿函数也是一个类。为什么不能是函数呢？

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 因为set是类模板，类模板的参数只能放数据类型，而函数不是数据类型，因此只能用仿函数（类），**

&nbsp;  &nbsp;  &nbsp;  &nbsp; Set容器排序默认升序，想要降序，在插入之前指定排序规则：

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 用仿函数myCompare重载()运算符，指定排序规则。注意重载函数后的const。**
<br>
```cpp
class myCompare
{
public:
//重载()运算符，要加const
	bool operator() (int v1, int v2)	const
{
	return v1 > v2;
}
}

set<int, myCompare> s1;
```


<br>

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 当Set中插入自定义数据类型，如自定义的Person类时，需自定义排序规则。**
<br>

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include<iostream>
#include<set>

using namespace std;


class Person
{
public:
	Person(string name, int age)
	{
		this->m_Name = name;
		this->m_Age = age;
	}

	string m_Name;
	int m_Age;
};

class myCompare
{
public:
	bool operator() (const Person& p1,const Person& p2)  const
	{
		if (p1.m_Age > p2.m_Age)
		{
			return true;
		}
		return false;
	}
};

void test()
{
	set<Person, myCompare> s1;

	Person p1("w", 10);
	Person p2("a", 20);
	Person p3("n", 30);
	Person p4("g", 40);
	Person p5("x", 50);
	Person p6("u", 60);

	s1.insert(p1);
	s1.insert(p2);
	s1.insert(p3);
	s1.insert(p4);
	s1.insert(p5);
	s1.insert(p6);

	for (set<Person, myCompare>::iterator it = s1.begin(); it != s1.end(); ++it)
	{
		cout << "姓名： " << (*it).m_Name << " 年龄：" << (*it).m_Age << endl;
	}

}

int main()
{
	test();

	return 0;
}
```





