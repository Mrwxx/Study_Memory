## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 类的静态成员
 &nbsp;  &nbsp;  &nbsp;  &nbsp;大家应该都知道**静态变量**，就是在变量前面加上**static**，类的静态成员也是同样的道理，在类的成员函数与成员变量前面加上static声明为类的静态成员。和静态变量的原理一样，不管这个类创建了多少个对象，静态成员只有**一个拷贝**，这个拷贝是被所有这个类的对象**共享**的。
<br>
## 二. 类的静态成员变量
### （一）定义
 &nbsp;  &nbsp;  &nbsp;  &nbsp;类的静态变量即是在类中，用关键字static声明成员变量。无论建立了多少个对象，都只有一个静态变量的拷贝。静态成员变量，不属于某一个类对象，而只是属于这个类，对于所有类对象时共享的。
### （二）重点记忆
#### 1. 类的静态变量的分配空间
 &nbsp;  &nbsp;  &nbsp;  &nbsp;类的静态变量，在编译阶段就已经分配了空间，即还没有创建类对象时，就已经分配空间。
   &nbsp;  &nbsp;  &nbsp;  &nbsp; **理解**：同static变量一样，类的静态变量也是在编译阶段就分配好了空间。
#### 2. 在为类对象分配空间中不包括静态成员所占空间。
&nbsp;  &nbsp;  &nbsp;  &nbsp;**理解**：因为静态变量并不属于某个类对象，它是共享的，因此独立的类对象分配空间是不包括静态成员的空间的。
#### 3. 类的静态变量可以通过类对象来访问，也可以通过类名直接访问。
**理解：**
##### （1）**通过类对象访问是正常访问**。
&nbsp;  &nbsp;  &nbsp;  &nbsp;如下面代码所示：（我直接写类和调用了）

```cpp
#include<iostream>
using namespace std;

class Person{
public:
	Person(){};

	static int a;
}
int Person::a = 0;
int main()
{
	Person p1;
	p1.a  = 100;
	Person p2;
	p2.a  = 200;
	cout << "a1 = " << p1.a<<endl;
	cout <<"a2 = " << p2.a << endl;
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;**输出结果**是什么呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp;当然是：
**&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;	a1 = 200;
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;	a2 = 200;**
&nbsp;  &nbsp;  &nbsp;  &nbsp;因为 a 是类的静态变量，是共享的，所以更改的都是同一个值。
##### **（2）通过类名直接访问**
&nbsp;  &nbsp;  &nbsp;  &nbsp;如何通过类名直接访问呢？可以用下面代码所示调用：

```cpp
Person::a;
```

**&nbsp;  &nbsp;  &nbsp;  &nbsp;因为static变量并不属于任何一个类对象，但是它属于这个类，因此可以直接用类名调用。**

#### 4. 静态成员变量必须在类中声明，在类外定义。
&nbsp;  &nbsp;  &nbsp;  &nbsp;静态变量在类中声明static,为什么在类外定义呢？类中能否定义？答案是不行的。来看看代码。

```cpp
#include<iostream>
using namespace std;

class Person{
public:
	Person(int pa)
	{
		a  = pa;
	};
	static int a;
}

int main()
{
	cout <<Person::a << endl;	//错误，无法访问到a
}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;代码中，**我们用 Person::a 的方式直接访问类的静态变量，是不会调用构造函数的**，因此类中的 a 是没有定义的。所以最好是在类外定义类的静态变量。
<br>
## 三. 类的静态成员函数
### （一）. 定义
&nbsp;  &nbsp;  &nbsp;  &nbsp;在类定义中，用static声明的成员函数为静态成员函数。使用方式与静态成员变量一样，都是通过类对象访问和类名直接访问。
### （二）意义
&nbsp;  &nbsp;  &nbsp;  &nbsp;类的静态成员函数的意义在于管理静态成员变量，完成对静态成员变量的封装。
### （三）. 重点记忆
#### 1. 静态成员函数只能访问静态成员变量，不能访问普通成员变量。
&nbsp;  &nbsp;  &nbsp;  &nbsp;**理解**：static成员函数是是**共享**的，存储在一块空间中，而普通的成员变量是属于每个类对象的，每个类对象存储在独立的空间中，**若在静态成员函数中直接访问普通成员变量，那么它将无法区分普通的成员变量属于哪个类对象。**

#### 2.普通成员函数可访问静态成员变量、也可以访问普通成员变量。
&nbsp;  &nbsp;  &nbsp;  &nbsp;**理解**：静态成员变量只有一份存储空间，普通成员函数可以直接识别，不会混淆。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;以上就是类中的静态成员变量以及静态成员函数的理解，原创不易，多多鼓励！！！**


