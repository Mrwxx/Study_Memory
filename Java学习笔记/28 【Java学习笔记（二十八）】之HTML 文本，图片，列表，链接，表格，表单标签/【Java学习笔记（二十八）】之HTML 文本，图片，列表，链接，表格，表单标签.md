## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	软件架构
### （一）	C/S
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Client / Server，客户端/服务器端。使用者使用客户端，向服务器端发送请求。缺点是需要安装，部署，维护麻烦。优点是用户的体验更好。
<br>


### （二）	B/S
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Browser/Server，浏览器/服务器端。使用者只需要浏览器通过不同的URL访问不同的服务器端。优点是开发，安装，部署，维护简单，缺点是对硬件要求高，若应用过大，则用户体验降低。
<br>




## 二．	服务器端资源分类
### （一）	静态资源
#### 1.	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;使用静态网页开发技术发布的资源，即所有的用户访问得到的结果都是一样的。当用户请求静态资源时，服务器直接将静态资源发送给浏览器，浏览器中内置了静态资源解析器。
<br>



#### 2.	静态网页技术
 &nbsp;  &nbsp;  &nbsp;  &nbsp;HTML,:搭建基础网页，展示页面的内容
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;CSS：美化页面
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;JavaScript：控制页面的元素，让页面有一些动态效果。
<br>



### （二）	动态资源
#### 1.	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;动态网页即时发布的资源，不同用户访问结果不同。当用户请求动态资源时，服务器会将动态资源转换为静态资源再发送给浏览器。
<br>



#### 2.	动态网页技术
 &nbsp;  &nbsp;  &nbsp;  &nbsp;jsp/servlet, php, asp

<br>



## 三．	HTML
### （一）	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Hyper Text Markup Language 超文本标记语言。
<br>



### （二）	超文本
 &nbsp;  &nbsp;  &nbsp;  &nbsp;用超链接的方式，将各种不同的文字信息组织在一起的网状文字，超文本可以将文字，音频，视频组织在一起。
<br>



### （三）	标记语言
 &nbsp;  &nbsp;  &nbsp;  &nbsp;所谓标记语言，指的是由标签构成的语言，如html, xml，标记语言不是编程语言，它没有逻辑性，只要写上标记，就会生成效果。
<br>



### （四）	HTML语法
#### 1.	后缀名
 &nbsp;  &nbsp;  &nbsp;  &nbsp;html文档后缀xx.html或者xx.htm
<br>



#### 2.	标签
##### （1）	围堵标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;有开始标签和结束标签，如`<html> </html>`
<br>



##### （2）	自闭和标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;开始标签和结束标签在一起，如 

```html
<br />
<br>
```
<br>





#### 3.	注意
 &nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	标签是可以嵌套的

 &nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	在开始标签中可以定义属性，属性是由键值对构成的，值需要用引号，如 

```html
 <font color=’red’> </font>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	html的标签不区分大小写，建议小写

<br>



## 四．	HTML标签
### （一）	文件标签
#### 1.	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;文件标签是构成HTML文档的最基本的标签。
<br>



#### 2.	html
 &nbsp;  &nbsp;  &nbsp;  &nbsp;html文档的根标签
<br>



#### 3.	head
 &nbsp;  &nbsp;  &nbsp;  &nbsp;头标签，用于指定html文档的一些属性，引入外部的资源
<br>



#### 4.	title
 &nbsp;  &nbsp;  &nbsp;  &nbsp;标题标签
<br>



#### 5.	body
 &nbsp;  &nbsp;  &nbsp;  &nbsp;体标签
<br>



#### 6.	!DOCTYPE html
 &nbsp;  &nbsp;  &nbsp;  &nbsp;html5中定义该文档为html文档
<br>



### （二）	文本标签
#### 1.	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;和文本相关的标签
<br>



#### 2.	注释
 &nbsp;  &nbsp;  &nbsp;  &nbsp;<！--注释内容-->
<br>



#### 3.	h1- h6
 &nbsp;  &nbsp;  &nbsp;  &nbsp;标题标签，h1是最大的标题，h6是最小的标题
<br>



#### 4.	p
 &nbsp;  &nbsp;  &nbsp;  &nbsp;段落标签，表名这是一个段落。
<br>



#### 5.	br
 &nbsp;  &nbsp;  &nbsp;  &nbsp;换行标签
<br>



#### 6.	hr
 &nbsp;  &nbsp;  &nbsp;  &nbsp;表示一条水平线
<br>



#### 7.	b
 &nbsp;  &nbsp;  &nbsp;  &nbsp;加粗字体
<br>



#### 8.	i
 &nbsp;  &nbsp;  &nbsp;  &nbsp;斜体字体
<br>



#### 9.	font
 &nbsp;  &nbsp;  &nbsp;  &nbsp;字体标签
<br>



#### 10.	center
 &nbsp;  &nbsp;  &nbsp;  &nbsp;文本居中
<br>



#### 11.	属性定义
##### （1）	color
 &nbsp;  &nbsp;  &nbsp;  &nbsp;颜色的英文单词
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;rgb，如rgb(0, 0, 255)
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;十六进制，#值1#值2#值3，如#FF00FF
<br>



##### （2）	width
 &nbsp;  &nbsp;  &nbsp;  &nbsp;数值，单位是px(像素)，如 width=’20’
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;比例，该元素占父元素的比例，如 width=’50%’
<br>



### （三）	图片标签
#### 1.	概述

```html
<img>展示一张图片。
```
<br>



#### 2.	src
 &nbsp;  &nbsp;  &nbsp;  &nbsp;图片的路径地址，一般为相对路径。”./”代表当前路径， “../”代表上一级目录。
<br>



#### 3.	alt
 &nbsp;  &nbsp;  &nbsp;  &nbsp;当图片加载失效时，显示的字符
<br>



#### 4.	align
 &nbsp;  &nbsp;  &nbsp;  &nbsp;图片所在的位置，”left”, “center”, “right”
<br>



### （四）	列表标签
#### 1.	有序列表

```html
<ol> 
	<li> 
	</li>
</ol>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;有顺序的列表，<li>标签为列表中的每个事务
<br>



#### 2.	无序列表

```html
<ul> 
	<li> 
	</li>
</ul>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;无顺序的列表，li 标签表示列表中的每个事务
<br>



### （五）	链接标签
#### 1.	超链接

```html
<a></a>表示一个超链接。
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;属性href指定访问资源的URL（统一资源定位符）
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;属性target指定新页面为“_self”代表不打开新窗口，“_blank”代表打开新窗口
<br>



### （六）	块标签
#### 1.	内联标签

```html
<span></span>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;配合CSS使用，称为内联标签，文本信息在一行内展示，在CSS中设置span的样式。

<br>


#### 2.	块级标签

```html
<div></div>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;配合CSS使用，称为块级标签，每一个div占满一整行。
<br>



### （七）	语义化标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Html5中为了提高程序的可读性，提供的一些标签，没有具体的含义。


```html
<header> 表示头部
```

	

```html
<footer> 表示尾部
```

### （八）	表格标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;1.	table : 定义表格的标签

 &nbsp;  &nbsp;  &nbsp;  &nbsp;2.	tr：定义表格中行的标签
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;3.	td：定义表格中每一行的单元格的标签
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;4.	th：定义表头的单元格的标签
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;5.	caption: 表格标题标签
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;6.	thead:表格的头部标签
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;7.	tbody: 表格的体标签
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;8.	tfoot:表格的尾部标签
<br>



### （九）	表单标签
#### 1.	form标签概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;`<form></form>` 标签是用于定义表单的，可以定义一个范围，代表采集用户数据的范围。
<br>



#### 2.	form标签属性
 &nbsp;  &nbsp;  &nbsp;  &nbsp;action : 指定提高数据的URL
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;method: 指定提交数据的方式，常用的为get或者post
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;name：表当中的数据要想被提交，必须指定name属性
<br>



#### 3.	get和post的区别
get : 

```html
请求参数会在地址栏中显示
请求参数大小是有限制的
get请求不安全
```

post：
	

```html
请求参数不会在地址栏中显示，会封装到请求体中
请求参数的大小没有限制
Post请求比较安全
```
<br>



### （十）	表单项标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;表单中每一项的标签。
 <br>



#### 1.	input标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;输入标签，用户的输入信息标签。可以通过type属性值，改变元素展示的样式。
 
**type属性**：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;text：文本输入框，默认值
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;password：密码输入框，密文展示
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;radio：单选框。向实现单选框效果，则多个单选框的name属性必须相同。每个单选框都有value属性，指定该单选框被选中后提交的值；checked属性指定默认值。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;checkbox：复选框。同理，value属性指定提交值，checked属性指定默认值。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;file: 文件选择框
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;hidden: 隐藏域，用于提交一些信息
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;submit: 提交按钮，用于提交表单
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;button: 普通按钮，与js配合使用
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;image: 图片提交按钮
<br>



**label标签**：指定输入项的文字描述信息。当label标签的for属性和input的id属性对应相等时，则点击label区域，input框会获取焦点，即鼠标焦点转到input框中。

<br>



#### 2.	select标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;下拉列表标签，子标签`<option>`指定每个列表项

<br>



#### 3.	textarea标签
 &nbsp;  &nbsp;  &nbsp;  &nbsp;文本域标签，设置一个输入文本的区域。

<br>



## 五．	table网页布局
### （一） 一行一个单元格 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;使用table来布局网页时，如果某一行只有一个单元格，则使用

```html
<tr>
		<td>
		</td>
</tr>
```
<br>



### （二）一行多个单元格
 &nbsp;  &nbsp;  &nbsp;  &nbsp;如果某一行有多个单元格，则在td中嵌套一个table，在table中定义多个单元格
<br>



```html
<tr>
		<td>
			<table>
			</table>
		</td>
</tr>
```





