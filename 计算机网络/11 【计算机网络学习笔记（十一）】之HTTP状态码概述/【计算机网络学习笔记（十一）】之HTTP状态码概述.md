@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	HTTP状态码
### （一）	状态码概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;状态码的作用是当额客户端向服务器端发送请求时，描述返回的请求结果，借助状态码，用户可以知道服务器端是否正常处理了请求。状态码的格式以3位数字和原因短语组成，如200 OK，数字中的第一位指定了响应类别，后两位没有分类。
<br>


### （二）	状态码分类
#### 1.	1XX
&nbsp;  &nbsp;  &nbsp;  &nbsp;信息性状态码，表示接受的请求正在处理。
<br>


#### 2.	2XX
&nbsp;  &nbsp;  &nbsp;  &nbsp;成功状态码，表示请求正常处理完毕。
<br>


#### 3.	3XX
&nbsp;  &nbsp;  &nbsp;  &nbsp;重定向状态码，表示需要附加操作以完成请求。
<br>


#### 4.	4XX
&nbsp;  &nbsp;  &nbsp;  &nbsp;客户端错误状态码，表示客户端请求有错误。
<br>


#### 5.	5XX
&nbsp;  &nbsp;  &nbsp;  &nbsp;服务器错误状态码，表示服务器内部错误。
<br>


### （三）	2XX 成功
#### 1.	200 OK
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示从客户端发送来的请求在服务器端被正常处理了。
<br>


#### 2.	204 No Content
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示服务器接收的请求已经成功处理，但是在返回的响应报文中不包含实体的主体部分。一般在只需要从客户端往服务器发送消息，而对客户端不需要返回新消息时使用。
<br>


#### 3.	206 Partial Content
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示客户端进行了范围请求，服务器成功执行了这部分的GET请求，响应报文中包含了由Contenet-Range指定范围的实体内容。
<br>


### （四）	3XX 重定向
#### 1.	301 Moved Permanently
&nbsp;  &nbsp;  &nbsp;  &nbsp;永久性重定向，表示请求的资源已经被分配了新的URI，以后应该使用资源现在的URI。
<br>


#### 2.	302 Found
&nbsp;  &nbsp;  &nbsp;  &nbsp;临时性重定向，表示请求的资源已经被分配了新的URI，希望用户能够使用新的URI访问，与301的区别是它还有可能继续变化，不是永久性的。
<br>


#### 3.	303 See Other
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示请求的资源存在着另一个URI，应该使用GET方法定向获取请求的资源。它与302作用相同，但是303指定了使用GET方法获取资源。
<br>


#### 4.	304 Not Modified
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示客户端发送了附带条件的请求，服务器允许请求访问资源，但是因为请求没有满足条件，直接返回了304，不包含任何响应的主体部分。
<br>


### （五）	4XX客户端错误
#### 1.	400 Bad Request
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示请求报文中存在语法错误，需要修改请求内容再次发送。
<br>


#### 2.	401 Unauthorized
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示发送的请求需要有通过HTTP认证（BASIC认证，Digest认证）的认证信息，如果之前已经进行了一次请求，则表示用户认证失败。401响应中必定包含一个适用于被请求资源的WWW-Authenticate首部用以质询用户信息，浏览器会弹出认证窗口。
<br>


#### 3.	403 Forbidden
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示对请求资源的访问被服务器拒绝了，服务器端可以给出拒绝的理由，如未获得资源的访问权限等。
<br>


#### 4.	404 Not Found
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示服务器上无法找到请求的资源。
<br>


### （六）	5XX服务器错误
#### 1.	500 Internal Server Error
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示服务器端在执行请求时发生错误，很大可能是服务器的BUG。
<br>


#### 2.	503 Service Unavailable
&nbsp;  &nbsp;  &nbsp;  &nbsp;表示服务器暂时处于超负载或停机维护，现在无法处理请求。

