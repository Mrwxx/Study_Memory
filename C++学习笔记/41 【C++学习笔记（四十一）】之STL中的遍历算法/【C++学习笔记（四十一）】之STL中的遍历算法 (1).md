## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. for_each 算法
### （一） 用途：
&nbsp;  &nbsp;  &nbsp;  &nbsp;**遍历算法 遍历容器元素**，参数如下：
<br>

	@param beg 开始迭代器
	@param end 结束迭代器
	@param _callback  函数回调或者函数对象
	@return 函数对象

<br>

### （二）使用函数，仿函数都可以作为遍历的规则

```cpp
void myPrint(int v)
{
	cout <<v << endl;
}

class myPrint01
{
public:
	void operator()(int v)
	{
		cout <<v <<endl;
	}
}

void test()
{
	vector<int> v;
	for(int i = 0 ; i < 10; ++i)
	{
		v.push_back(i);
	}
	for_each(v.begin(), v.end(), myPrint);
	for_each(v.begin(), v.end(), myPrint01());
}
```
<br>

### （三）用返回的函数对象存储信息
<br>

```cpp
class myPrint
{
public:
	void operator()(int v)
	{
		cout <<v <<endl;
	}
	int m_Count;
}

void test()
{
	vector<int> v;
	for(int i = 0; i < 10; ++i)
	{
		v.push_back(i);
	}
//for_each返回函数对象，其中可以存储信息
	myPrint print = for_each(v.begin(), v.end(), myPrint());
	cout << print.m_Count << endl;
}
```

<br>

### （四）可以用适配器绑定参数

<br>

```cpp
class myPrint : public binary_function<int, int , void>
{
public:
	void operator()(int val)	const 
	{
		cout << val << endl
	}
}

void test()
{
	vector<int> v;
	for(int i = 0; i <10; ++i)
	{
		v.push_back(i);
	}
	for_each(v.begin(), v.end(), bind2nd(myPrint(),100) );
}
```


<br>


## 二. transform算法
### （一）第一个用途：
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;**将指定区间元素搬运到另一容器中。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;
&nbsp;  &nbsp;  &nbsp;  &nbsp;**不会分配内存，需要我们手动分配**。
<br>

	@param beg1 源容器开始迭代器
	@param end1 源容器结束迭代器
	@param beg2 目标容器开始迭代器
	@param _cakkback 回调函数或者函数对象
	@return 返回目标容器迭代器
<br>


```cpp
class Transform
{
public:
	int operator() (int val)
	{
		return val + 10;
	}
}

void test()
{
	vector<int> v;	//原容器
	for(int i = 0; i < 10; ++i)
	{
		v.push_back(i);
	}
	vector<int> vTarget;	//目标容器
	vTarget.resize(v.size());	//开辟空间
	transform(v.begin(), v.end(), vTarget.begin(), Transform());
}
```
<br>

### （二）第二个用途
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;**transform可以将两个容器的数据运算后搬运到目标容器中**
<br>


```cpp
class Transform
{
public:
	int operator() (int val, int val2)
	{
		return val + val2;
	}
}

void test()
{
	vector<int> v1;
	vector<int> v2;
	for(int i = 0; i < 10; ++i)
	{
		v1.push_back(100 + i);
		V2.push_back(200 + i);
	}
	vector<int> vTarget;
	vTarget.resize(v1.size());

	transform(v1.begin(), v1.end(), v2.begin(), v2.end(), vTarget.begin(), Transform());

}
```

