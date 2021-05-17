## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. QMainWindow

&nbsp;  &nbsp;  &nbsp;  &nbsp;之前有说到，QMainWindow类主要承担的是菜单栏，工具栏，状态栏，铆接部件，核心部件等窗口部件，如下图所示：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072612453529.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
<br>

## 二．	菜单栏
&nbsp;  &nbsp;  &nbsp;  &nbsp;**一个主窗口最多只有一个菜单栏，位于主窗口的顶部，标题下面。**

### （一）创建菜单栏
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过QMainWindow类的menubar（）函数获取主窗口菜单栏指针

```cpp
QMenuBar *	menuBar() const
```

<br>

### （二）创建菜单
&nbsp;  &nbsp;  &nbsp;  &nbsp;调用QMenu的成员函数addMenu来添加菜单

```cpp
QMenu* addMenu(const QString & title)
QMenu* addMenu(const QIcon & icon, const QString & title)
```

<br>

### （三）创建菜单里的选项
&nbsp;  &nbsp;  &nbsp;  &nbsp;**QAction是抽象类，当我们把QAction对象添加到菜单中，就显示成菜单项，但是，它并不是一个专门的菜单项类。**

```cpp
QAction* addMenu(QMenu * menu)
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;调用QMenu的成员函数addAction来添加菜单项

```cpp
QAction* activeAction() const
QAction* addAction(const QString & text)
QAction* addAction(const QIcon & icon, const QString & text)
QAction* addAction(const QString & text, const QObject * receiver,
 const char * member, const QKeySequence & shortcut = 0)
QAction* addAction(const QIcon & icon, const QString & text, 
const QObject * receiver, const char * member, 
const QKeySequence & shortcut = 0)
```
<br>


## 三．	工具栏
&nbsp;  &nbsp;  &nbsp;  &nbsp;主窗口可以有多个工具栏，可以调整工具栏的位置。

### （一）	创建工具栏
&nbsp;  &nbsp;  &nbsp;  &nbsp;调用QMainWindow类的addToolBar（）函数获取主窗口的工具条对象，每增加一个工具条都需要调用一次该函数。
<br>

### （二）	插入属于工具栏的选项
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过QToolBar类的addAction函数添加。
<br>

### （三）	移动工具栏
&nbsp;  &nbsp;  &nbsp;  &nbsp;工具栏的停靠区域由QToolBar的allowAreas决定


停靠区域如下：
1. Qt::LeftToolBarArea		停靠在左侧
2. Qt::RightToolBarArea		停靠在右侧
3. Qt::TopToolBarArea		停靠在顶部
4. Qt::BottomToolBarArea   	停靠在底部
5. Qt::AllToolBarAreas		以上四个位置都可停靠
<br>

## 四．	状态栏
&nbsp;  &nbsp;  &nbsp;  &nbsp;**主窗口底部的状态栏，最多只有一个。**  **QStatusBar类**

```cpp
//添加小部件
void addWidget(QWidget * widget, int stretch = 0)
//插入小部件
int	insertWidget(int index, QWidget * widget, int stretch = 0)
//删除小部件
void removeWidget(QWidget * widget)
```

<br>

## 五．	铆接部件
&nbsp;  &nbsp;  &nbsp;  &nbsp;即**浮动窗口**，能够再窗口中浮动并提取出来，可以有多个。

```cpp
QDockWidget * dock = new QDockWidget("标题",this);
addDockWidget(Qt::LeftDockWidgetArea,dock);
dock->setAllowedAreas(Qt::LeftDockWidgetArea | Qt::RightDockWidgetArea | Qt::TopDockWidgetArea);  设置区域范围
```
<br>

## 六．	核心部件
&nbsp;  &nbsp;  &nbsp;  &nbsp;核心部件是一个应用程序的主体，即主要的操作都在核心部件上面。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如，记事本用的是QTextEdit做核心部件：

```cpp
QTextEdit * edit = new QTextEdit(this);
setCentralWidget(edit);
```
<br>

## 七．	资源文件
**&nbsp;  &nbsp;  &nbsp;  &nbsp;资源文件用于将程序运行时所需要的资源以二进制的形式存储于可执行文件内部。如果你将资源以资源文件形式存储，它是会编译到可执行文件内部。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;资源文件就是程序中需要使用的图片等资源，如何使用资源文件呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp;在新建文件时，添加Qt Resource File，生成qrc文件，然后为每种资源设置前缀，前缀即资源的分类，之后可以将需要的资源导入。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;导入资源文件到项目中的格式
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;: + 前缀名/文件名** 

&nbsp;  &nbsp;  &nbsp;  &nbsp;如下所示：

```cpp
ui->actionNew->setIcon(QIcon(“:/Image/Luffy.png”));
```

<br>

## 八．	代码
<br>

```cpp
#include "mainwindow.h"
#include<QMenuBar>
#include<QToolBar>
#include<QStatusBar>
#include<QLabel>
#include<QDockWidget>
#include<QTextEdit>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
{
    resize(600, 400);

    //创建菜单栏，只能有一个
    QMenuBar *bar = menuBar();
    //将菜单栏放入窗口中
    this->setMenuBar(bar);

    //创建文件菜单
    QMenu *fileMenu = bar->addMenu("文件");
    QMenu *editMenu = bar->addMenu("编辑");

    //添加菜单里的选项
    QAction *newAction = fileMenu->addAction("新建");
    //添加分割线
    fileMenu->addSeparator();

    QAction *openAction = fileMenu->addAction("打开");

    //工具栏， 可以有多个
    QToolBar *toolBar = new QToolBar(this);
    addToolBar(Qt::LeftToolBarArea,toolBar);

    //只允许左右停靠工具栏
    toolBar->setAllowedAreas(Qt::LeftToolBarArea | Qt::RightToolBarArea);

    //设置工具栏浮动
    toolBar->setFloatable(false);

    //工具栏添加菜单项
    toolBar->addAction(newAction);
    toolBar->addAction(openAction);

    //状态栏,只能有一个
    QStatusBar * stBar = statusBar();
    setStatusBar(stBar);

    //状态栏中显示信息
    QLabel *label = new QLabel("提示信息",this);
    //添加提示信息到左侧
    stBar->addWidget(label);

    //添加提示信息到右侧
    QLabel *label2 = new QLabel("右侧提示信息",this);
    stBar->addPermanentWidget(label2);

    //铆接部件，浮动窗口,可以有多个
    QDockWidget *dock = new QDockWidget;
    //添加到窗口中
    addDockWidget(Qt::BottomDockWidgetArea,dock);

    //核心部件,只能一个
    //文本编辑框
    QTextEdit *edit = new QTextEdit;
    setCentralWidget(edit);
}

MainWindow::~MainWindow()
{
}
```





