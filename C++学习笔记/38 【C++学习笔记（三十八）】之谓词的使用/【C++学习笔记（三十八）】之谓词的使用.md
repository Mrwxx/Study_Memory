## 一. 谓词

&nbsp;  &nbsp;  &nbsp;  &nbsp;谓词是指普通函数或者重载的operator()**返回值时bool类型的函数对象（仿函数）**。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果operator接受一个参数，那么称为一元谓词；如果接受两个参数，那么称为二元谓词。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;谓词的主要作用是一个判断语句，用来作为排序的判断。**
<br>

## 二. 谓词的使用
### （一）一元谓词的使用，判断查找
<br>

```cpp
class GreaterThan20
{
public:
	bool operator() (int val)
	{
		return val > 20;
	}
}


vector<int> v;
v.push_back(10);
v.push_back(20);
v.push_back(30);

//查找第一个大于20的数字
//注意传入的是匿名函数，不是之前直接传入函数名
vector<int>::iterator pos = find_if(v.begin(), v.end(), GreaterThan20());
if(pos != v.end())
{
	cout << “找到大于20的数字: “ << *pos << endl;
}
```

### （三）二元谓词的使用， 排序规则
<br>

```cpp
class MyCompare
{
public:
	bool operator() (int v1, int v2)
	{
		return v1 > v2;
	}
}

vector<int> v;
v.push_back(10);
v.push_back(20);
v.push_back(30);

//排序,传入的是匿名对象
sort(v.begin(), v.end(), MyCompare());
//传入的是匿名函数 lambda表达式
for_each(v.begin(), v.end(), [](int val) {cout << val << endl;} );


```



