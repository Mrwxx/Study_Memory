## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 多态
**&nbsp;  &nbsp;  &nbsp;  &nbsp;多态是面向对象程序设计语言中数据抽象和继承之外的第三个基本特征。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;多态性改善了代码的可读性和组织性，同时也使创建的程序具有可扩展性，项目不仅在最初创建时期可以扩展，而且当项目在需要有新的功能时也能扩展。
<br>
## 二. 多态的类型
**&nbsp;  &nbsp;  &nbsp;  &nbsp;C++支持静态多态（编译时多态）和动态多态（运行时多态）。
&nbsp;  &nbsp;  &nbsp;  &nbsp;静态多态： 运算符重载和函数重载
&nbsp;  &nbsp;  &nbsp;  &nbsp;动态多态： 继承和虚函数**

**理解：**
&nbsp;  &nbsp;  &nbsp;  &nbsp;静态多态即函数地址是在编译阶段就已经绑定好了，如函数的重载，传入的参数在编译阶段就已经绑定了。
&nbsp;  &nbsp;  &nbsp;  &nbsp;动态多态即函数地址在运行阶段才能决定，这就是晚绑定。如虚函数，在函数运行时才知道传入的是什么类型的参数。
**&nbsp;  &nbsp;  &nbsp;  &nbsp;用一句话总结多态：父类的引用或指针指向子类对象。**
<br>
## 三. 虚函数原理解析
<br>

### （一）虚函数

&nbsp;  &nbsp;  &nbsp;  &nbsp;C++动态多态性是通过虚函数来实现的，虚函数允许子类（派生类）重新定义父类（基类）成员函数

&nbsp;  &nbsp;  &nbsp;  &nbsp;对于特定的函数进行动态绑定，c++要求在基类中声明这个函数的时候使用virtual关键字,动态绑定也就对virtual函数起作用.

### （二）虚函数的注意事项：
1. 为创建一个需要动态绑定的虚成员函数，可以简单在这个函数声明前面加上virtual关键字，定义时候不需要.
2. 如果一个函数在基类中被声明为virtual，那么在所有派生类中它都是virtual的.
3. 在派生类中virtual函数的重定义称为重写(override).
4. Virtual关键字只能修饰成员函数.
5. 构造函数不能为虚函数
<br>
### (三)父类声明虚函数
**&nbsp;  &nbsp;  &nbsp;  &nbsp;在父类Animal中speak函数声明为虚函数后，该虚函数结构发生了改变，生成了vfptr(virtual function pointer)虚函数表指针，指向了vftable(virtual function table)虚函数表，该表中存的是 &Animal::speak ，这是Animal的函数speak的入口地址。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200717100613124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
 

```cpp
class Animal
{
public:
	virtual void speak() {}
}
```

<br>

### （四）子类继承父类，未定义同名函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;子类Cat继承父类Animal，当子类Cat没有定义自己的speak函数时，Cat继承了父类的vfptr和vbtable，但是Cat的vfptr并不是指向父类的vbtable，而是指向自己的vbtable。**这是在Cat对象创建的时候，调用构造函数时，Cat的vfptr就指向了自己的vftable。存储的还是继承的Animal的函数speak的入口地址。**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200717123407822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

```cpp
class Cat : public Animal
{
}
```
<br>

### （五）子类继承父类，且定义同名函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;如果子类Cat定义了自己的speak函数，**那么子类Cat就会生成 &Cat::speak覆盖掉自己的vftable中的 &Animal::speak**， 但是父类中的vftable 还是&Animal::speak。即Cat子类的vftable存储的变成了Cat子类的speak函数的入口地址，但是父类Animal的vftable存储的依然是Animal的函数speak的入口地址。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;重写：子类Cat写父类Animal的虚函数speark，这种写法叫重写。重写规定返回值，参数个数，类型，顺序都要相同。**

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200717100632329.png)

```cpp
class Cat : public Animal
{
public:
	void speak(){} 
}
```

