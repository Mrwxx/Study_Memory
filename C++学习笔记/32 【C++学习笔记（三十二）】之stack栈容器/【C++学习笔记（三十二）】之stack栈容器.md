## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．Stack栈容器

**&nbsp;  &nbsp;  &nbsp;  &nbsp;栈容器是单口的数据结构，特点是先进后出。数据只能从栈顶进入，也只能从栈顶取出，除此以外，没有其他方法可以存取栈容器的数据。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;**因此，栈容器是没有遍历操作的**，它只能从栈顶存数据，取数据，且只能在取出数据的情况下获取该数据；没有像遍历那样，能够在不取出数据的情况下获取容器中的任意数据。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;因此，栈容器没有迭代器。**
<br>

## 二. 栈容器的常用操作
<br>


### （一）	stack构造函数
<br>

```cpp
stack<T> stkT;//stack采用模板类实现， stack对象的默认构造形式： 
stack(const stack &stk);//拷贝构造函数
```
<br>

### （二）	stack赋值操作
<br>

```cpp
stack<T> stkT;//stack采用模板类实现， stack对象的默认构造形式： 
stack(const stack &stk);//拷贝构造函数
```
<br>

### （三）	stack数据存取操作
<br>

```cpp
push(elem);//向栈顶添加元素
pop();//从栈顶移除第一个元素
top();//返回栈顶元素
```
<br>

### （四）	stack大小操作
<br>

```cpp
empty();//判断堆栈是否为空
size();//返回堆栈的大小
```



