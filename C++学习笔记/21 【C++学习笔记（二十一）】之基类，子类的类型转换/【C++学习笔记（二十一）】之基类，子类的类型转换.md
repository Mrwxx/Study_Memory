## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 普通基类，子类的转换

&nbsp;  &nbsp;  &nbsp;  &nbsp;子类是由继承于基类，通常子类的内容要比基类多一些，因此子类开辟的内存要比基类大一些。

### (一) 基类 -> 子类（向下类型转换）
&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们要把基类强转为子类时，由于子类的内存空间一般大一些，那么通过强转，当指针在基类的空间中移动时，如果超过了基类的空间大小，那么就有可能出现错误。

### （二）子类 -> 基类（向上类型转换）
&nbsp;  &nbsp;  &nbsp;  &nbsp;当把子类强转为基类时，由于子类的空间大于基类的空间，那么不论指针如何移动，都不会超过子类的内存空间大小，因此是安全的。


## 二. 多态基类，子类的转换

&nbsp;  &nbsp;  &nbsp;  &nbsp;当发生了多态时，如 Animal是基类，Cat是子类

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;Animal * animal = new Cat

&nbsp;  &nbsp;  &nbsp;  &nbsp;那么 new Cat 就已经申请了Cat子类大小的空间了，不论是向上类型转换还是向下类型转换，都是安全的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如：向下类型转换

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;Cat * cat = (Cat *) animal

&nbsp;  &nbsp;  &nbsp;  &nbsp;都是安全的。

