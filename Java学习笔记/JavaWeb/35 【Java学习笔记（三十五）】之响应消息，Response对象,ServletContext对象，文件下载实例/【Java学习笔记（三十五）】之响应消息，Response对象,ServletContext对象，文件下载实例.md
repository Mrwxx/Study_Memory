## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	响应消息
### （一）	响应消息的组成
1.	请求行

2.	请求头

3.	请求空行

5.	请求体
<br>


### （二）	响应行
#### 1.	组成

```java
协议/版本 响应状态码 状态码描述
```
<br>



#### 2.	响应状态码
 &nbsp;  &nbsp;  &nbsp;  &nbsp;服务器告诉客户端浏览器本次请求和响应的状态。状态码一般都是3位数字，分为5大类：
##### （1）	1xx
 &nbsp;  &nbsp;  &nbsp;  &nbsp;一百多的数字代表着服务器接收到了客户端的请求，但是没有接收完成。
<br>



##### （2）	2xx
 &nbsp;  &nbsp;  &nbsp;  &nbsp;二百多的数字代表着请求成功。
<br>



##### （3）	3xx
 &nbsp;  &nbsp;  &nbsp;  &nbsp;三百多的数字代表着重定向，客户端向服务器发送请求，服务器将该请求重定向到其他位置，如缓存中（304）
<br>



##### （4）	4xx
 &nbsp;  &nbsp;  &nbsp;  &nbsp;四百多的数字代表着客户端的请求错误，如404代表着客户端的请求路径中没有对应的资源，如405代表着请求方式没有对应的doxxx方法
<br>



##### （5）	5xx
 &nbsp;  &nbsp;  &nbsp;  &nbsp;五百多的数字代表着服务器端的错误，如500代表着服务器内部异常。
<br>



### （三）	响应头
#### 1.	格式

```java
头名称：值
```
<br>



#### 2.	常见响应头
##### （1）	Content-Type
 &nbsp;  &nbsp;  &nbsp;  &nbsp;服务器告诉客户端本次响应体数据格式以及编码格式

```java
Content-Type: text/html;charset=UTF-8
```

<br>



##### （2）	Content-disposition
 &nbsp;  &nbsp;  &nbsp;  &nbsp;服务器告诉客户端以什么方式打开响应体数据。默认值为 in-line，代表着在当前页面打开
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;值为attachment;filename=xx代表着以附件形式打开响应体，一般用于文件下载中。
<br>




## 二．	Response对象
### （一）	设置Response对象
#### 1.	设置响应行
##### （1）	格式

```java
HTTP/1.1 200 ok
```
<br>



##### （2）	设置状态码

```java
setStatus(int src)
```
<br>



#### 2.	设置响应头

```java
setHeader(String name, String value)
```
<br>



#### 3.	设置响应体
##### （1）	获取输出流
字符输出流： `printWriter getWriter()`
字节输出流： `ServletOutputStream getOutputStream()`
<br>



##### （2）	使用输出流，将数据输出到客户端浏览器中
<br>



### （二）	重定向实例
#### 1.	思路
 &nbsp;  &nbsp;  &nbsp;  &nbsp;设置状态码为302，返回另一个资源的路径。
<br>



#### 2.	代码实现

```java
//访问demo1，会跳转到demo2
System.out.println("demo1");
//1.设置状态码为302
resp.setStatus(302);
//2. 设置响应头location
resp.setHeader("location", "/Login/Response/responseDemo2");
//简单的重定向方法
resp.sendRedirect(“/Login/Response/responseDemo2”)
```
<br>



#### 3.	重定向的特点
（1）	地址栏发生变化

（2）	重定向可以访问其他服务器的资源

（3）	重定向是两次请求，因此不能像转发一样使用request域来共享数据
<br>



### （三）	路径的写法
#### 1.	相对路径
 &nbsp;  &nbsp;  &nbsp;  &nbsp;以./开头的路径，要找到当前资源路径和目标资源路径之间的相对位置关系。
如：

```java
./index.html
```
<br>



#### 2.	绝对路径
 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过绝对路径可以确定唯一的资源，可以省略协议和域名，直接以/开头写绝对路径，如：

```java
/login/response
```
<br>



#### 3.	路径的使用
 &nbsp;  &nbsp;  &nbsp;  &nbsp;对于客户端和服务器，我们提供的路径是不一样的。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;若路径是提供给客户端浏览器的，则需要添加虚拟目录（项目的访问路径），这个虚拟目录可以通过动态获取，如：

```java
request.getContextPath()
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;若路径是提供给服务器的，则不需要添加虚拟路径，直接写路径即可。
<br>



### （四）	输出字符数据实例
#### 1.	中文字符的乱码问题
 &nbsp;  &nbsp;  &nbsp;  &nbsp;当我们想要输出中文字符到浏览器中时，会出现乱码问题，这是因为系统使用的编码表和Tomcat返回的字符输出流的编码表不一致。Tomcat服务器返回的字符输出流的编码表为ISO-8859-1，不支持中文，因此我们需要设置字符输出流的编码为支持中文的编码表，如GBK，”utf-8”，然后通过设置response的响应头的content-type属性，将使用的编码表告诉浏览器，建议浏览器使用该编码表解码。这样，乱码问题就解决了。
<br>



#### 2.	思路
（1）	设置输出流的编码方式

（2）	获取响应的字符输出流

（3）	输出数据到浏览器中
<br>



#### 3.	实现代码

```java
//设置流的默认编码
resp.setCharacterEncoding("GBK");
//告诉浏览器，服务器发送的消息体数据的编码格式
resp.setHeader("content-type", "text/html;charset=GBK");
//简单的设置编码方式
resp.setContentType("text/html;charset=GBK");

//获取字符输出流
PrintWriter pw = resp.getWriter();
//输出数据
pw.write(" 你的 world");
```
<br>



### （五）	输出字节数据到浏览器
#### 1.	思路
 &nbsp;  &nbsp;  &nbsp;  &nbsp;获取字节输出流
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;输出数据到浏览器
<br>



#### 2.	代码实现

```java
resp.setContentType("text/html;chareset=utf-8");

//获取字节输出流
ServletOutputStream sos = resp.getOutputStream();
//输出数据
sos.write("你好".getBytes());
```

<br>



## 三．	ServletContext对象
### （一）	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;ServletContext对象代表着整个web应用，可以和程序的容器（服务器）进行通信。
<br>



### （二）	获取ServletContext对象
#### 1.	通过request对象获取

```java
request.getServletContext();
```
<br>



#### 2.	通过HttpServlet获取

```java
this.getServletContext()；
```
<br>



### （三）	ServletContext对象的功能
#### 1.	获取MIME类型
 &nbsp;  &nbsp;  &nbsp;  &nbsp;MIME类型：在互联网通信过程中定义的一种文件数据类型
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;格式：大类型/小类型
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;如：`text/html ; image/jpeg`

```java
//获取ServletContext对象
ServletContext context = this.getServletContext();
//定义文件名称
String filename = "a.jpg";
//获取MIME类型
String mimeType = context.getMimeType(filename);
System.out.println(mimeType);
```
<br>



#### 2.	域对象共享数据
 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过ServletContext对象来设置参数，共享数据。用这个方式时要谨慎，因为ServletContext对象的范围是面向所有用户的所有请求数据，并不安全。

```java
setAttribute(String name, Object value)
getAttribute(String name)
removeAttribute(String name)
```
<br>



#### 3.	通过ServletContext对象获取文件服务器路径
 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过ServletContext对象可以获取项目中文件的路径。
##### （1）	文件在web目录下
 &nbsp;  &nbsp;  &nbsp;  &nbsp;当文件在web目录下时，直接在前面加上反斜杠获取路径:

```java
String a = context.getRealPath(“/a.txt”);
```
<br>



##### （2）	文件在web目录下的文件夹内
 &nbsp;  &nbsp;  &nbsp;  &nbsp;若文件在web目录下的WEB-INF文件夹中，则在反斜杠后再添加WEB-INF文件夹的名称即可：

```java
String b = context.getRealPath(“/WEB-INF/b.txt”);
```
<br>



##### （3）	文件在src目录下
 &nbsp;  &nbsp;  &nbsp;  &nbsp;若文件在src目录下，则需要到web目录下的WEB-INF文件夹中的classes文件夹中查找：

```java
String c = context.getRealPath(“/WEB-INF/classes/c.txt”);
```
<br>




## 四．	文件下载实例
### （一）	需求分析
#### 1.	在页面中显示超链接
 &nbsp;  &nbsp;  &nbsp;  &nbsp;超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如果不能解析，则弹出下载提示框。
<br>



#### 2.	点击超链接后弹出下载提示框
 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过设置响应头的资源打开方式为attachment即可：

```java
content-disposition:attachment;filename=xxx
```
<br>



#### 3.	点击下载

### （二）	实现步骤
#### 1.	定义页面
 &nbsp;  &nbsp;  &nbsp;  &nbsp;编辑超链接href属性，将href指向一个Servlet对象，不论下载什么资源都使用这个Servlet对象，并传递资源的名称filename。

```html
<a href=”/Response/downloadServlet?filename=1.jpg”>图片</a>
```
<br>



#### 2.	实现Servlet
##### （1）	获取href中资源文件的名称

```java
String filename = req.getParameter("filename");
```
<br>



##### （2）	获取资源文件的真实路径

```java
ServletContext context = this.getServletContext();
String realPath = context.getRealPath("/img/"+filename);
```
<br>



##### （3）	使用字节输入流将资源文件加载到内存中

```java
//用字节流关联，将文件字节读入到内存汇总
FileInputStream fis = new FileInputStream(realPath);
```

<br>


##### （4）	指定response对象的响应头
	
```java
//设置response的响应头
//设置响应头的文件类型 content-type
String mimeType = context.getMimeType(filename);
resp.setHeader("content-type", mimeType);
//设置响应头的打开方式 content-disposition
resp.setHeader("content-disposition", "attachment;filename=" + filename);
```
<br>



##### （5）	将数据写出到response输出流中

```java
//将输入流的数据写出到输出流中
//为response对象关联一个输出流
ServletOutputStream sos = resp.getOutputStream();
byte[] buff = new byte[1024 * 8];
int len = 0;
while((len = fis.read(buff)) != 0){
    sos.write(buff, 0, len);
}
```
<br>




#### 3.	解决中文编码问题
##### （1）	获取user-agent请求头、
 &nbsp;  &nbsp;  &nbsp;  &nbsp;获取客户度使用的浏览器版本信息：

```java
String agent=  request.getHeader(“user-agent”);
```
<br>



##### （2）	使用DownLoadUtils工具类方法编码文件名
 &nbsp;  &nbsp;  &nbsp;  &nbsp;根据不同的版本信息，设置filename的不同编码方式：

```java
filename = DownLoadUtils.getFileName(agent, filename);
```






