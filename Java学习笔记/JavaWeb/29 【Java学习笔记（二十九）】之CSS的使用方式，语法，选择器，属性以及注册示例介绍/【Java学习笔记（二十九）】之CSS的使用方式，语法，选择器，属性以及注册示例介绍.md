## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	CSS介绍
### （一）	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Cascading Style Sheets 层叠样式表，将多个样式作用在同一个html的元素上，同时生效。
<br>


### （二）	优势
 &nbsp;  &nbsp;  &nbsp;  &nbsp;将内容与样式的控制进行分离，降低耦合度，提高开发效率。
<br>

## 二．	CSS的三种使用方式

### （一）内联样式
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在内容的标签上使用style属性指定css代码。这种方式css代码只能作用于该代码段。
如：

```html
<div style=”color:red;”>hello world</div>
```
<br>



### （二）内部样式
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在head标签中定义style标签，style标签的标签体内容就是css代码。这种方式css样式只能作用于该HTML文件。
 
如：

```html
<style>
		<div>{
			color:red;
		}
</style>

<div>hello world</.div>
```
<br>



### （三）外部样式
 &nbsp;  &nbsp;  &nbsp;  &nbsp;单独定义css资源文件，在html的head标签内定义link标签，引入外部的资源文件。这种方式css样式能够作用于所有的文件。Link标签中的href表示css文件的URL。

a.css文件：

```html
div{
	color:green;
}
```

html文件：

```html
<link rel=”stylesheet” href=”./a.css”>
<div> hello world</ div> 
```

也可以用以下方式引入css文件：

```html
<style>
	@import “./a.css”;
</style>
```

<br>



## 三．	CSS的语法
### （一）	格式

```html
选择器{
	属性名1：属性值1；
	属性名2：属性值2；
}
```
<br>



### （二）选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;选择器：筛选具有相似特征的元素。
#### 1.	基础选择器
##### （1）	Id选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;用”#”号选择具体的id属性值的元素，一个html页面中id值唯一

```html
#id属性值{}
```
<br>



##### （2）	元素选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;选择具有相同标签名称的元素

```html
标签名称{}
```
<br>



##### （3）	类选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;用”.”选择具有相同的class属性值的元素

```html
.class属性值{}
```
<br>



##### （4）	选择器优先级
 &nbsp;  &nbsp;  &nbsp;  &nbsp;id选择器>类选择器>元素选择器
<br>



#### 2.	扩展选择器
##### （1）	并集选择器

```html
选择器1，选择器2{}
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;中间没有空格，两个选择器都选中。

<br>


##### （2）	子选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;筛选选择器1元素下的所有选择器2元素，中间有空格。如 a b{}，则选择a的所有b后代元素。

```html
选择器1 选择器2{}
```
<br>



##### （3）	父选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;筛选选择器1的子选择器2，只选择一代，中间用>号。如 a > b{}，则只选择a的后一代b元素。

```html
选择器1 > 选择器2{}
```
<br>



##### （4）	属性选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;选择元素名称，属性名=属性值的元素。

```html
元素名称[属性名=“属性值”]{}
```
<br>



##### （5）	伪类选择器
 &nbsp;  &nbsp;  &nbsp;  &nbsp;每个元素都有不同的状态分为

```html
 初始化状态link
 被访问过的状态visited
 正在访问状态active
 鼠标悬浮状态hover
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;可以通过伪类选择器设置元素在不同的状态的样式。

```html
元素：状态{}
a:link{}
```
<br>



### （二）	属性
#### 1.	文本

```html
font-size:字体大小
color: 文本颜色
text-align: 对齐方式
line-height:行高
```
<br>



#### 2.	背景

```html
background
```
<br>



#### 3.	边框

```html
border:设置边框属性
```
<br>



#### 4.	尺寸

```html
width:宽度
height:高度
```
<br>



#### 5.	盒子模型
 &nbsp;  &nbsp;  &nbsp;  &nbsp;用于控制布局。

```html
margin:外边距，即该盒子与上一级的盒子的距离
padding:内边距，即该盒子与同级的盒子之间的距离
float：浮动，可让多个盒子浮动到一行 
```

<br>



## 四．	CSS实现登录界面
### （一）	布局
 &nbsp;  &nbsp;  &nbsp;  &nbsp;整体为一个盒子，盒子中包括了三个小盒子，三个盒子浮动到同一行中。中间一个盒子嵌套一个form表单，表单中嵌套一个table表。
<br>



### （二）	代码实现
<br>



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body{
            background: url("./image/register_bg.png");
        }

        .rg_layout{
            width:900px;
            height:500px;
            boder: 8px solid #EEEEEE;
            background-color: white;
            margin: auto;
            margin-top: 15px;
        }

        .rg_left{
            float:left;
            margin:15px;
        }
        .rg_left > p:first-child{
            color:#FFD026;
            font-size:20px;
        }

        .rg_left > p:last-child{
            color:#A6A6A6;
            font-size: 20px;
        }

        .rg_center{
            float:left;
        }

        .rg_right{
            float:right;
            margin:15px;
        }
        .rg_right > p:first-child{
            font-size: 15px;
        }
        .rg_right p a{
            color:red;
        }

        .td_left{
            width:100px;
            text-align: right;
            height:45px;
        }

        .td_right{
            padding-left:50px;
        }

        #username,#checkcode,#birthday,#password,#email,#name,#phone{
            width:251px;
            height:32px;
            border:1px solid #A6A6A6;
            border-radius:5px;
            paddint-left:10px;
        }

        #checkcode{
            width:110px;
        }

        #checkcode{
            hieght:32px;
            vertical-align: middle;
        }

        #btn_sub{
            width:150px;
            height:40px;
            background-color:#FFD026;
            border: 1px solid #FFD026;
        }
    </style>
</head>
<body>
<!--    注册页面-->
    <div class="rg_layout">
<!--        三个小框-->
        <div class="rg_left">
            <p>新用户注册</p>
            <p>USER REGISTER</p>
        </div>
<!--        表单-->
        <div class="rg_center">
            <div class="rg_form">
                <form action="#" method="post">
                    <table>
<!--                       第一行-->
                        <tr>
                            <td class="td_left">
                                <label for="username">用户名</label>
                            </td>
                            <td class="td_right">
                                <input type="text" name="username" id="username" placeholder="请输入用户名">
                            </td>
                        </tr>
<!--                        第二行-->
                        <tr>
                            <td class="td_left">
                                <label for="password">密码</label>
                            </td>
                            <td class="td_right">
                                <input type="password" name="password" id="password" placeholder="请输入密码">
                            </td>
                        </tr>
<!--                        Email-->
                        <tr>
                            <td class="td_left">
                                <label for="email">Email</label>
                            </td>
                            <td class="td_right">
                                <input type="text" name="email" id="email" placeholder="请输入邮箱">
                            </td>
                        </tr>

<!--                        姓名-->
                        <tr>
                            <td class="td_left">
                                <label for="name">姓名</label>
                            </td>
                            <td class="td_right">
                                <input type="text" name="name" id="name" placeholder="请输入姓名">
                            </td>
                        </tr>

<!--                        手机号-->
                        <tr>
                            <td class="td_left">
                                <label for="phone">手机号</label>
                            </td>
                            <td class="td_right">
                                <input type="text" name="phone" id="phone" placeholder="请输入手机号">
                            </td>
                        </tr>

<!--                        性别-->
                        <tr>
                            <td class="td_left">
                                <label>性别</label>
                            </td>
                            <td class="td_right">
                                <input type="radio" name="gender" value="male">男
                                <input type="radio" name="gender" value="female">女
                            </td>
                        </tr>
                        
<!--                        出生日期-->
                        <tr>
                            <td class="td_left">
                                <label for="birthday">出生日期</label>
                            </td>
                            <td class="td_right">
                                <input type="date" name="birthday" id="birthday" placeholder="请输入出生日期">
                            </td>
                        </tr>
                        
                        <tr>
                            <td class="td_left">
                                <label for="checkcode">验证码</label>
                            </td>
                            <td class="td_right">
                                <input type="text" name="checkcode" id="checkcode" placeholder="请输入验证码">
                                <img src="./image/verify_code.jpg" alt="验证码">
                            </td>
                        </tr>
<!--                        注册-->
                        <tr>
                            <td colspan="2" align="center">
                                <input type="submit" value="注册" id="btn_sub">
                            </td>
                        </tr>
                    </table>
                </form>
            </div>
        </div>

        <div class="rg_right">
            <p>已有账号? <a href="#">立即登录</a></p>
        </div>

    </div>
</body>
</html>
```




