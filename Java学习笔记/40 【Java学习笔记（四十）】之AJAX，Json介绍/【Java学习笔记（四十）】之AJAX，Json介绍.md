## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	AJAX
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Asynchronous JavaScript And XML，异步的JavaScript和XML。所谓的异步指的是客户端不需要等待服务器端的响应，在服务器daun处理请求的过程中，客户端可以进行其他的操作。
<br>


### （二）	优势
&nbsp;  &nbsp;  &nbsp;  &nbsp;Ajax是一种在无需重新加载整个网页的情况下，更新部分网页的技术。通过在后台与服务器进行少量数据交换，Ajax可以使网页实现异步更新。
<br>



### （三）	实现方式
#### 1.	JQuery实现
##### （1）$.ajax()
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过$.ajax()方法发送异步请求，在ajax方法的参数中传入一些参数，如请求路径，请求方式，请求参数，响应成功后的回调函数…
<br>



```javascript
$.ajax({
	url:”ajaxServlet”,		//请求路径
	type:”POST”,				//请求方式
	data:{“username”:”jack”, “age”:23},//请求参数
	success:function(data){
		alert(data);
	}						//响应成功后的回调函数
	error:function(){
		alert(“error”);	
	}						//请求响应出现错误的回调函数
	datatype:”text”			//响应的数据类型
})
```
<br>



##### （2）$.get()
&nbsp;  &nbsp;  &nbsp;  &nbsp;用于发送get请求

如：

```javascript
$.get(
	“ajaxServlet”, 
	{username:”rose”}, 
	function(data){
		alert(data);
	}. 
	“text”
);
```
<br>



##### （3）$.post
&nbsp;  &nbsp;  &nbsp;  &nbsp;发送POST请求，参数与$.get方法一样的。

<br>



## 二．	JSON
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;JavaScript Object Notation，JavaScript对象表示法。JSON将数据打包成对象进行传输，多用于存储和交换文本信息，进行数据的传输。语法类似于XML，但是比XML更小，更快，更易解析。
<br>



### （二）	语法
#### 1.	基本语法
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	数据在名称/值对中：json数据是由键值对构成的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;键用引号（单双）引起来，也可以不用引号；

&nbsp;  &nbsp;  &nbsp;  &nbsp;值可以嵌套json对象以及json对象数组


&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	数据由逗号分隔，多个键值对由逗号分隔。

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	使用花括号将json对象括起来。

&nbsp;  &nbsp;  &nbsp;  &nbsp;（4）	方括号保存json对象数组
<br>



#### 2.	获取json对象数据
##### （1）	json对象.键名
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个键名不用带引号。

```javascript
persons.name
```
<br>



##### （2）	json对象[“键名”]

```javascript
person[“name”]
```
<br>



### （三）	json数据和java对象的相互转换
#### 1.	Java对象转换为json数据
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	导入json的解析器，如Jsonlib, Gson, fastjson, Jackson.这里我们导入jackson的Jar包

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	创建Jackson的核心对象，ObjectMapper

```java
ObjectMapper mapper = new ObjectMapper();
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	调用ObjectMapper的相关方法进行转换

第一个转换方法：


```java
writeValue(参数1， obj)
```

参数1：
&nbsp;  &nbsp;  &nbsp;  &nbsp;File, 将obj对象转换为json字符串，并保存到指定的File文件中。

&nbsp;  &nbsp;  &nbsp;  &nbsp;Writer，将obj对象转换为json字符串，并将json数据填充到字符输出流中。

&nbsp;  &nbsp;  &nbsp;  &nbsp;OutputStream，将obj对象转换为json字符串，并将json数据填充到字节输出流中。

```java
mapper.writeValue(new File(“./a,txt”), p);
mapper.writeValue(new FileWriter(“./b.txt”), p);
```

<br>


第二个转换方法：
```java
writeValueAsString(obj)
```

将obj对象转换为json字符串

```java
String json = mapper.writeValueAsString(p)
```
<br>



##### （4）	注解的使用
&nbsp;  &nbsp;  &nbsp;  &nbsp;@JsonIgnore 用于忽略某些键值对，将该注解放置于Java对象的属性定义之上，那么转换成json数据时，就会自动忽略该属性：

```java
@JsonIgnore
private String name;
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;@JsonFormat， 用于调整属性的格式：
		

```java
@JsonFormat(pattern=“yyyy-MM-dd”)
private Date birthday;
```
<br>



##### （5）	list和map转换为json
&nbsp;  &nbsp;  &nbsp;  &nbsp;list转换为json数据的格式为json对象数组。

&nbsp;  &nbsp;  &nbsp;  &nbsp;map转换为json数据的格式为json对象。
<br>



#### 2.	json数据转换为Java对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	导入jackson解析器的jar包

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	创建jackson的核心对象 ObjectMapper

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	调用ObjectMapper的如下方法：

&nbsp;  &nbsp;  &nbsp;  &nbsp;readValue(json字符串数据，Java对象的Class)

```java
String json = “{\”gender\”:\”男\”, \”name\’:\”张三\”}”;
ObjectMapper mapper = readValue(json, Person.class);
```








