## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 虚析构函数

&nbsp;  &nbsp;  &nbsp;  &nbsp;子类继承了父类，但是普通析构函数是不会调用子类的析构的，所以可能会导致内存释放地不干净。我们需要使用虚析构函数来解决。

&nbsp;  &nbsp;  &nbsp;  &nbsp;写法：

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;Virtual ~Animal(){}

**&nbsp;  &nbsp;  &nbsp;  &nbsp;虚析构函数是为了解决基类的指针指向派生类对象，并用基类的指针删除派生类对象。**


## 二. 纯虚析构函数

&nbsp;  &nbsp;  &nbsp;  &nbsp;写法：

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;声明：Virtual ~Animal() = 0

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;实现：Animal::~Animal(){…}

&nbsp;  &nbsp;  &nbsp;  &nbsp;纯虚析构，需要声明，也需要实现。类内声明，类外实现。
&nbsp;  &nbsp;  &nbsp;  &nbsp;若类中有纯虚函数，则类也是抽象类，不可实例化。


## 三. 代码：

```cpp
class People{
public:
	People(){
		cout << "构造函数 People!" << endl;
	}
	virtual void showName() = 0;
	virtual ~People(){
		cout << "析构函数 People!" << endl;
	}
};

class Worker : public People{
public:
	Worker(){
		cout << "构造函数 Worker!" << endl;
		pName = new char[10];
	}
	virtual void showName(){
		cout << "打印子类的名字!" << endl;
	}
	~Worker(){
		cout << "析构函数 Worker!" << endl;
		if (pName != NULL){
			delete pName;
		}
	}
private:
	char* pName;
};

void test(){

	People* people = new Worker;
	people->~People();
}
```

