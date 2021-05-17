## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 按钮

### （一）QPushButton

&nbsp;  &nbsp;  &nbsp;  &nbsp;**查询帮助文档**可以看到 QPushButton这个类的相关信息如下：

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200725161626432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)



&nbsp;  &nbsp;  &nbsp;  &nbsp;1. Header:代表需要添加的头文件

&nbsp;  &nbsp;  &nbsp;  &nbsp;2. qmake代表所属的模块，在.pro工程配置文件中添加，widgets是默认添加的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;3. Inherits : 继承的父类 QAbstractButton

&nbsp;  &nbsp;  &nbsp;  &nbsp;4. Inherited By: 它的子类是QCommandLinkButton
<br>

### （二）如何在窗口中添加按钮？

&nbsp;  &nbsp;  &nbsp;  &nbsp;按钮其实就是一个QPushButton的类对象，生成这个类对象后，我们需要将**按钮和窗口联系**起来，窗口是一个QWidget类对象，这个类是QPushButton类的基类，所以我们需要将**按钮和父类用setParent联系起来**。

&nbsp;  &nbsp;  &nbsp;  &nbsp;其他的常用操作还有设置显示文字，移动按钮位置，修改按钮尺寸，修改窗口大小等等
<br>

```cpp
#include<QPushButton>

Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
    //按钮
    QPushButton * btn = new QPushButton;
    //设置父类关系,因为是widget基类的
    btn->setParent(this);
    //设置显示文字
    btn->setText("第一个按钮");  // char* 隐式转换为QString

    //第二种按钮创建方式
    QPushButton * btn2 = new QPushButton("第二个按钮", this);
    this->resize(600, 600);
    //移动第二个按钮
    btn2->move(100, 100);

    //重置按钮尺寸
    btn2->resize(200,200);

    //修改窗口标题
    setWindowTitle("窗口");

    //设置固定的窗口
    setFixedSize(600,600);

}
```



