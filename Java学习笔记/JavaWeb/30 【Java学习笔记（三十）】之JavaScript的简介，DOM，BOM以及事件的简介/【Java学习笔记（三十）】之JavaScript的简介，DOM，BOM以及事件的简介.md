## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	JavaScript的简介
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;一门客户端脚本语言，所谓的脚本语言指的是不需要编译的语言，可以直接被浏览器解析执行。
<br>


### （二）	功能
&nbsp;  &nbsp;  &nbsp;  &nbsp;增强用户与html页面的交互过程，控制html元素，让页面有动态的效果。

<br>



## 二．	JavaScript与HTML的结合方式
### （一）	在html内部定义js
&nbsp;  &nbsp;  &nbsp;  &nbsp;在html中定义<script>标签，标签体内容就是js代码。
<br>



### （二）	定义js文件，导入HTML中
&nbsp;  &nbsp;  &nbsp;  &nbsp;定义js文件，在HTML中用<script>标签的src属性导入js文件。

<br>



## 三．	JavaScript的数据类型
### （一）	基本数据类型
#### 1.	number
&nbsp;  &nbsp;  &nbsp;  &nbsp;数字类型，分为整数，小数，NaN（非数字）
<br>



#### 2.	string
&nbsp;  &nbsp;  &nbsp;  &nbsp;字符串类型，用双引号引起来。
<br>



#### 3.	Boolean
&nbsp;  &nbsp;  &nbsp;  &nbsp;布尔类型，true和false。
<br>



#### 4.	null
&nbsp;  &nbsp;  &nbsp;  &nbsp;一个对象为空的占位符
<br>



#### 5.	undefined
&nbsp;  &nbsp;  &nbsp;  &nbsp;如果一个变量没有被初始化，则被默认赋值为undefined。
<br>




## 四．	JavaScript变量
### （一）	强类型语言
&nbsp;  &nbsp;  &nbsp;  &nbsp;强类型语言在开辟变量存储空间时，定义了空间将来存储数据的数据类型，只能存储固定类型的数据。如Java
<br>



### （二）	弱类型语言
&nbsp;  &nbsp;  &nbsp;  &nbsp;在开辟变量存储空间时，不定义空间要存储数据的数据类型，可以存放任意数据类型
<br>



### （三）	变量语法

```html
var 变量名 = 初始化值；
```
<br>



## 五．	函数对象
### （一）创建函数

```html
function 方法名称(形参){
		方法体
}
```
<br>



### （二）特点
1. 方法定义的形参类型不用写，返回值类型也不用写

2. 方法是一个对象，如果定义名称相同的方法，前面的方法会被覆盖

4. 在方法声明中有一个内置数组arguments存储所有的实参

<br>



## 六．	DOM
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Document Object Model，文档对象模型。将标记语言文档的各个组成部分，封装为对象。可以使用这些对象，对标记语言文档进行操作。
<br>



### （二）DOM三个标准
#### 1.  核心DOM
&nbsp;  &nbsp;  &nbsp;  &nbsp;针对任何结构化文档的标准模型，以下是文档的各个组成部分：

```html
Document :文档对象
Element:元素对象
Attribute:属性对象
Text:文本对象
Comment:注释对象
Node:节点对象，其他5个对象的父对象
```
<br>



#### 2.XML DOM
&nbsp;  &nbsp;  &nbsp;  &nbsp;针对XML文档的标准模型
<br>



#### 3.	HTML DOM
&nbsp;  &nbsp;  &nbsp;  &nbsp;针对HTML文档的标准模型
<br>



### （三）核心DOM模型
#### 1.	Document文档对象
##### （1）	创建
&nbsp;  &nbsp;  &nbsp;  &nbsp;在 HTML DOM 模型中可以使用window对象来获取

```html
window.document
document
```
<br>



##### （2）	方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取Element对象：

```html
getElementById():根据id属性获取元素对象
getElementsByTagName():根据元素名称获取元素对象们，返回值是数组
getElementsByClassName():根据class属性值获取元素对象们
getElementsByName():根据name属性值获取元素对象们
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建其他DOM对象：

```html
createAttribute(name)
crreateComment()
createElement()
createTextNode()
```
<br>



#### 2.	Node节点对象
##### （1）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;所有的DOM对象都可以被认为是一个节点。
<br>



##### （2）方法

```html
appendChild()：向节点的子节点列表的结尾添加新的子节点
removeChild():删除（并返回）当前节点的指定子节点
replaceChild()：用新节点替换一个子节点
```
<br>



### （四）	HTML DOM
#### 1.	innerHTML
&nbsp;  &nbsp;  &nbsp;  &nbsp;innerHTML用于标签体的设置和获取，直接使用innerHTML就可以修改元素的标签的设置。

如：将div通过innerHTML替换为文本框

```html
<div id=”div”></div>
<script>
	var div = document.getELementById(“div”);
	var innerHTML = div.innerHTML;
	div.innerHTML = “<input type=”text”>”;
</script>
```

如：通过innerHTML追加文本框

```html
div.innerHTML += “<input type=”text”>”;
```
<br>



### （五） 使用核心DOM的动态表格示例
#### 1.	添加表格
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	为添加按钮绑定点击事件

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	获取输入文本框的内容

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	创建表格的td标签，设置td标签的内容为文本框的内容

&nbsp;  &nbsp;  &nbsp;  &nbsp;（4）	创建tr标签

&nbsp;  &nbsp;  &nbsp;  &nbsp;（5）	将td作为tr的子标签

&nbsp;  &nbsp;  &nbsp;  &nbsp;（6）	获取table标签，将tr作为子标签添加到table中
<br>


#### 2.	删除表格
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过点击删除超链接删除该行数据，首先获取通过查找该超链接的元素，然后调用删除函数，删除该行。
<br>



#### 3.	代码实现
<br>



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>动态表格</title>
    <style>
        table{
            border: 1px solid;
            margin: auto;
            width: 500px;
        }

        td, th{
            text-align: center;
            border: 1px solid;
        }

        div{
            text-align: center;
            margin: 50px;
        }
    </style>
</head>
<body>
    <div>
        <input type="text" id="id" placeholder="请输入编号">
        <input type="text" id="name" placeholder="请输入姓名">
        <input type="text" id="gender" placeholder="请输入性别">
        <input type="button" value="添加" id="btn_add">
    </div>

    <table id="table">
        <caption>
            学生信息表
        </caption>
        <tr>
            <th>编号</th>
            <th>姓名</th>
            <th>性别</th>
            <th>操作</th>
        </tr>

        <tr>
            <td>1</td>
            <td>a</td>
            <td>男</td>
            <td><a href="javascript:void(0);" onclick="del(this);">删除</a></td>
        </tr>

        <tr>
            <td>2</td>
            <td>b</td>
            <td>男</td>
            <td><a href="javascript:void(0);" onclick="del(this);">删除</a></td>
        </tr>

        <tr>
            <td>3</td>
            <td>c</td>
            <td>女</td>
            <td><a href="javascript:void(0);" onclick="del(this);">删除</a></td>
        </tr>

    </table>

    <script>
        var btn_add = document.getElementById("btn_add");
        btn_add.onclick = function () {
            var id = document.getElementById("id").value;
            var name = document.getElementById("name").value;
            var gender = document.getElementById("gender").value;

            //获取表格
            var table = document.getElementById("table");

            //创建tr
            var tr = document.createElement("tr");

            //创建id的td
            var td_id = document.createElement("td");
            //创建id文本的文本节点
            var text_id = document.createTextNode(id);
            td_id.appendChild(text_id);
            tr.appendChild(td_id);

            //创建name的td
            var td_name = document.createElement("td");
            var text_name = document.createTextNode(name);
            td_name.appendChild(text_name);
            tr.appendChild(td_name);

            //创建性别的td
            var td_gender = document.createElement("td");
            var text_gender = document.createTextNode(gender);
            td_gender.appendChild(text_gender);
            tr.appendChild(td_gender);

            //创建超链接a的td
            var td_option = document.createElement("td");

            //创建超链接a
            var a = document.createElement("a");
            //给超链接添加属性
            a.setAttribute("href", "javascript:void(0);");
            a.setAttribute("onclick", "del(this)");
            var text_a = document.createTextNode("删除");
            a.appendChild(text_a);
            td_option.appendChild(a);
            tr.appendChild(td_option);

            table.appendChild(tr);
        }

        //删除函数
        function del(obj){
            //通过obj向上找到table,tr
            var table = obj.parentNode.parentNode.parentNode;
            var tr = obj.parentNode.parentNode;
            //通过table删除子节点tr，删除一行
            table.removeChild(tr);
        }

    </script>
</body>
</html>
```
<br>



### （六）使用innerHTML添加动态表格

&nbsp;  &nbsp;  &nbsp;  &nbsp;直接获取table的元素，通过innerHTML直接添加一行。

```html
//通过innerHTML添加
document.getElementById("btn_add").onclick = function () {
    var id = document.getElementById("id").value;
    var name = document.getElementById("name").value;
    var gender = document.getElementById("gender").value;

    //获取table
    var table = document.getElementsByTagName("table")[0];
    //追加一行
    table.innerHTML += "<tr>\n" +
            "<td>" + id + "</td>\n" +
            "<td>" + name + "</td>\n" +
            "<td>" + gender+ "</td>\n" +
            "<td><a href=\'javascript:void(0);\' onclick='del(this);\'>删除</a></td>\n" +
            "</tr>"
}
```
<br>



## 七．	事件
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;事件指的是通过鼠标，键盘等对页面进行操作，当执行了这些操作以后，就能够触发某些代码的执行。
1.	事件：某些操作，如单击，双击，按下键盘，移动鼠标

2.	事件源：组件，如：按钮，文本输入框

3.	监听器：执行的代码

5.	注册监听：将事件，事件源，监听器结合在一起，当事件源发生了某个事件时，则触发执行某个监听器代码。
<br>



### （二）	常见的事件
#### 1. 点击事件
&nbsp;  &nbsp;  &nbsp;  &nbsp;onclick:单击事件

&nbsp;  &nbsp;  &nbsp;  &nbsp;ondbclick:双击事件
<br>



#### 2.	焦点事件
&nbsp;  &nbsp;  &nbsp;  &nbsp;onblur:失去焦点

&nbsp;  &nbsp;  &nbsp;  &nbsp;onfocus:元素获得焦点
<br>



#### 3.	加载事件
&nbsp;  &nbsp;  &nbsp;  &nbsp;onload：一张页面或衣服图像完成加载
<br>



#### 4.	鼠标事件
&nbsp;  &nbsp;  &nbsp;  &nbsp;onmousedown:鼠标被按下

&nbsp;  &nbsp;  &nbsp;  &nbsp;onmouseup:鼠标被松开

&nbsp;  &nbsp;  &nbsp;  &nbsp;onmousemove:鼠标被移动

&nbsp;  &nbsp;  &nbsp;  &nbsp;onmouseout:鼠标从某元素移开

&nbsp;  &nbsp;  &nbsp;  &nbsp;onmouseover:鼠标移到某元素之上
<br>



#### 5．键盘事件
&nbsp;  &nbsp;  &nbsp;  &nbsp;onkeydown:某个键盘按键被按下

&nbsp;  &nbsp;  &nbsp;  &nbsp;onkeyup:某个键盘按键被松开

&nbsp;  &nbsp;  &nbsp;  &nbsp;onkeypress:某个键盘按键被按下并松开
<br>



#### 6.选中和改变
&nbsp;  &nbsp;  &nbsp;  &nbsp;onchange:域的内容被改变

&nbsp;  &nbsp;  &nbsp;  &nbsp;onselect:文本被选中
<br>



#### 7. 表单事件
&nbsp;  &nbsp;  &nbsp;  &nbsp;onsubmit:确认按钮被点击

&nbsp;  &nbsp;  &nbsp;  &nbsp;onreset:重置按钮被点击

<br>



### （三）将操作绑定事件
有以下两种方式：

1.	直接在html中，获取元素对象，指定事件属性，属性就是要执行的操作。

3.	通过js获取元素对象，指定事件属性，将操作设置为一个函数，传入该函数执行事件的操作。

<br>



## 八．	BOM
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Browser Object Model，浏览器对象模型。该模型将浏览器的各个组成部分封装成对象。
<br>



### （二）	BOM的组成
#### 1.	Navigator
&nbsp;  &nbsp;  &nbsp;  &nbsp;浏览器对象，表示整个浏览器
<br>



#### 2.	Window
&nbsp;  &nbsp;  &nbsp;  &nbsp;窗口对象，表示每个页面的窗口
<br>



#### 3.	Screen
&nbsp;  &nbsp;  &nbsp;  &nbsp;显示器屏幕对象

<br>


#### 4.	History
&nbsp;  &nbsp;  &nbsp;  &nbsp;窗口的历史记录对象
<br>



#### 5.	Location
&nbsp;  &nbsp;  &nbsp;  &nbsp;地址栏对象，记录了地址。
<br>



### （三）	Window窗口对象
#### 1.	创建
&nbsp;  &nbsp;  &nbsp;  &nbsp;window
<br>



#### 2.	方法
##### （1）	弹出框方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;Alert()：显示带有一段信息和一个确认按钮的警告框

&nbsp;  &nbsp;  &nbsp;  &nbsp;Confirm()：显示一段信息和确认，取消按钮的对话框。	
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;如果用户选择确定，则返回true
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;如果用户选择取消，则返回false

&nbsp;  &nbsp;  &nbsp;  &nbsp;Prompt()：显示可提示用户输入的对话框
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;返回值：获取用户的输入
<br>



##### （2）	打开关闭窗口
&nbsp;  &nbsp;  &nbsp;  &nbsp;Open()：打开一个新的浏览器窗口
&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;返回新的Window对象
		
&nbsp;  &nbsp;  &nbsp;  &nbsp;Close()：关闭调用的浏览器窗口
<br>


##### （3）	定时器
&nbsp;  &nbsp;  &nbsp;  &nbsp;setTimeout()：在指定毫秒后调用函数或者表达式
		&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;参数：js代码或者函数，毫秒值
		&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;返回值：唯一标识，用于取消定时器
		
&nbsp;  &nbsp;  &nbsp;  &nbsp;clearTimeout()：取消setTimeout()设置的定时器

&nbsp;  &nbsp;  &nbsp;  &nbsp;setInterval()：按照指定的周期调用函数

&nbsp;  &nbsp;  &nbsp;  &nbsp;clearInterval()：取消setInterval()设置的定时器
<br>



#### 3.	属性
##### （1）	获取其他BOM对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;history,location,Navigator,Screen对象都可通过Window对象获取，如：

```html
window.history;
```
<br>



##### （2）	获取DOM对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过window对象可以获取到document对象
<br>



#### 4.	特点
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	window对象不需要创建，可以直接使用，如

```html
window.方法名();
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	window引用可以省略，直接写方法名。
<br>



#### 5.	轮播图示例
<br>



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    //显示图片
    <img src="./img/banner_1.jpg" alt="兵马俑" id="img">

    <script>
        var num = 1;
        function f() {
            num++;
            if(num > 3){
                num = 1;
            }
            //获取img对象
            var img = document.getElementById("img");
            img.src = "./img/banner_" + num + ".jpg";
        }

        //定义定时器
        setInterval(f, 2000);
    </script>
</body>
</html>
```

<br>



### （四）	Location地址栏对象
#### 1.	获取location对象

```html
window.location
location
```
<br>



#### 2.	方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;reload() 重新加载当前文档
<br>



#### 3.	属性
&nbsp;  &nbsp;  &nbsp;  &nbsp;href:设置或返回完整的URL
<br>



#### 4.	地址栏示例
<br>



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <input type="button" id="btn" value="refresh">
    <input type="button" id="goBaidu" value="baidu">
    <script>
        //reload方法
        var btn = document.getElementById("btn");
        btn.onclick = function () {
            //刷新页面
            location.reload();
        }

        //获取地址的href
        var href = location.href;
        var goBaidu = document.getElementById("goBaidu");
        goBaidu.onclick = function(){
            location.href = "https://www.baidu.com";
        }

    </script>
</body>
</html>
```
<br>



### （五）	History历史记录对象
#### 1.	创建

```html
window.history
history
```
<br>



#### 2.	方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;back() 加载history列表中的前一个URL

&nbsp;  &nbsp;  &nbsp;  &nbsp;forward() 加载history列表中的下一个URL

&nbsp;  &nbsp;  &nbsp;  &nbsp;go() 加载history 列表中的某个具体页面
		&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;参数：
		&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;正数：前进几个历史记录
		&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;负数：后退几个历史记录
<br>



#### 3.	属性
&nbsp;  &nbsp;  &nbsp;  &nbsp;length 返回当前窗口历史列表中的URL数量
<br>



#### 4.	history示例
<br>



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>History</title>
</head>
<body>
    <input type="button" id="btn" value="history">
    <a href="./轮播图.html">轮播图</a>
    <input type="button" id="forward" value="forward">
    <input type="button" id="back" value="back">
    <script>
        var btn = document.getElementById("btn");
        btn.onclick = function(){
            var length = history.length;
            alert(length);
        }

        var forward = document.getElementById("forward");
        forward.onclick = function () {
            history.forward();
        }

        var back = document.getElementById("back");
        back.onclick = function(){
            history.back();
        }

    </script>
</body>
</html>
```














