## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. queue容器
<br>

**&nbsp;  &nbsp;  &nbsp;  &nbsp; queue队列容器是双口的数据结构，特点是先进先出，即先存进去的数据先取出。存数据的一端叫做队尾，取数据的一端叫做队头。就像排队一样，先来排队的人一定是先出去的。**

**&nbsp;  &nbsp;  &nbsp;  &nbsp;与stack栈容器一样，queue队列容器也是没有迭代器的，没有遍历功能**。

&nbsp;  &nbsp;  &nbsp;  &nbsp;原因也是差不多的，因为queue队列容器只允许从队头取元素，且只有在队头取出元素的情况下才能获取该元素，不像遍历能够在不取出元素的情况下获取元素。


**如下图所示：**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200722101621656.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
<br>

## 二．Queue队列容器的常用操作
<br>

### （一）queue构造函数

```cpp
queue<T> queT;//queue采用模板类实现，queue对象的默认构造形式：
queue(const queue &que);//拷贝构造函数
```
<br>

### （二）queue存取，插入和删除操作
<br>

```cpp
push(elem);//往队尾添加元素
pop();//从队头移除第一个元素
back();//返回最后一个元素
front();//返回第一个元素
```
<br>

### （三）queue赋值操作
<br>

```cpp
queue& operator=(const queue &que);//重载等号操作符
```
<br>

### （四）queue大小操作
<br>

```cpp
empty();//判断队列是否为空
size();//返回队列的大小
```




