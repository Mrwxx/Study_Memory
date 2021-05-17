
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">



## 一 . find算法
### （一）查找基本数据类型元素
<br>

```cpp
@param beg 容器开始迭代器
@param end 容器结束迭代器
@param value 查找的元素
@return 返回查找元素的位置
```
<br>



```cpp
void test()
{
	vector<int> v;
	for(int i = 0; i < 10; ++i)
	{
		v.push_back(i);
	}
	vector<int>::iterator pos = find(v.begin(), v.end(), 5);
	if( pos != v.end())
	{
		cout << “找到了数据” << *pos << endl;
	}
	else
		cout <<”未找到!” <<endl;
}
```
<br>

### （二）查找自定义元素
&nbsp;  &nbsp;  &nbsp;  &nbsp;**需要重载 ==运算符**

```cpp
class Person
{
public:
	Person(string name, int age)
	{
	this->m_Name = name;	
	this->m_Age  = age;
	}

	bool operator==(const Person&p)
	{
		if(this->m_Name == p.m_Name && this->m_Age == p.m_Age)
		return true;
	return false;
	}
	string m_Name;	
	string m_Age;
}

void test()
{
	vector<Person> v;
	Person p1(“a”, 1);
	Person p2(“b”, 2);
	v.push_back(p1);
	v.push_back(p2);
	vector<Person>::iterator pos = find(v.begin(), v.end(), p2);
}
```
<br>

## 二. find_if 算法

&nbsp;  &nbsp;  &nbsp;  &nbsp;**条件查找，通过回调函数或者谓词（返回bool类型的函数对象）**
<br>


	@param beg 容器开始迭代器
	@param end 容器结束迭代器
	@param  callback 回调函数或者谓词(返回bool类型的函数对象)
	@return bool 查找返回true 否则false


<br>

```cpp
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
}

class myCompare
{
public:
	bool operator()(Person * p1)
	{
	if(p1->m_Name == “b” && p1->m_Age == 2)
		return true;
	return false;
}

}
void test()
{
	vector<Person*>v;
	Person p1(“a”,1);
	Person p2(“b”, 2);
	v.push_back(&p1);
	v.push_back(&p2);

	Person *p = new Person(“b”, 2);
	vector<Person*>::iterator pos = find_if(v.begin(), v.end(), myCompare());
}
```


<br>

## 三. Adjacent_find算法
&nbsp;  &nbsp;  &nbsp;  &nbsp;**查找相邻重复元素**，返回相邻元素的第一个位置的迭代器
<br>

```cpp
@param beg 容器开始迭代器
@param end 容器结束迭代器
@param  _callback 回调函数或者谓词(返回bool类型的函数对象)
@return 返回相邻元素的第一个位置的迭代器
```

比较简单，不展示了…
<br>

## 四. Binary_search算法
&nbsp;  &nbsp;  &nbsp;  &nbsp;**二分法查找**，返回bool类型，查找是否成功。**限定在有序序列中使用**。
<br>


	@param beg 容器开始迭代器
	@param end 容器结束迭代器
	@param value 查找的元素
	@return bool 查找返回true 否则false
<br>

## 五. Count算法
&nbsp;  &nbsp;  &nbsp;  &nbsp;**统计元素出现次数**


	@param beg 容器开始迭代器
	@param end 容器结束迭代器
	@param  value回调函数或者谓词(返回bool类型的函数对象)
	@return int返回元素个数
<br>

## 六. Count_if算法
&nbsp;  &nbsp;  &nbsp;  &nbsp;同样是统计元素出现次数，配有回调函数或者谓词


	@param beg 容器开始迭代器
	@param end 容器结束迭代器
	@param  callback 回调函数或者谓词(返回bool类型的函数对象)
	@return int返回元素个数




