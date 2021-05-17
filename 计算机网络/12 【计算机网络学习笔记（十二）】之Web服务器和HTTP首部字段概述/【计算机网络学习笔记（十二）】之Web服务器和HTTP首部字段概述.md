@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	Web服务器
### （一）	单机托管多个域名
&nbsp;  &nbsp;  &nbsp;  &nbsp; HTTP/1.1规范允许一台HTTP服务器搭建多个Web站点，如提供Web托管服务的供应商，利用虚拟主机功能，在一台服务器上为多个客户服务。
<br>


### （二）	通信数据转发程序
&nbsp;  &nbsp;  &nbsp;  &nbsp; HTTP通信时，除了客户端和服务器外，还有一些用于通信数据转发的程序，如代理，网关，对到，将请求转发给通信线路上的下一个服务器，并且能够接受响应转发给客户端。
<br>


#### 1.	代理
##### （1）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 代理是一种具有转发功能的程序，接受请求给服务器，也可以接受响应给客户端。代理不会改变请求的URI，会直接发送给目标服务器，如果通信链路中有多级代理服务器，每次转发会附加Via首部标记经过的主机信息。
<br>


##### （2）	使用代理的原因
&nbsp;  &nbsp;  &nbsp;  &nbsp; 利用缓存技术减少通信流量，针对特定网站的访问控制。
<br>


##### （3）	代理分类
&nbsp;  &nbsp;  &nbsp;  &nbsp; 代理分类的基准有两个，一个是是否使用缓存，另一个是是否修改报文。
<br>


##### （4）	缓存代理
&nbsp;  &nbsp;  &nbsp;  &nbsp; 代理转发响应时，缓存代理会将资源的副本保存在代理服务器上。当相同的请求再次发送到代理时，就可以直接返回缓存资源了。
<br>


##### （5）	透明代理
&nbsp;  &nbsp;  &nbsp;  &nbsp; 转发请求或响应时，不会报文做任何加工的代理称为透明代理。
<br>


#### 2.	网关
&nbsp;  &nbsp;  &nbsp;  &nbsp; 网关能够转发通信数据，同时也可以处理数据。网关可以转换通信的协议，即它可以和非HTTP服务器通信，然后转换为HTTP协议。同时网关也可以提高通信的安全性，可以在客户端和网关之间的通信线路上加密确保连接的安全。
<br>


#### 3.	隧道
&nbsp;  &nbsp;  &nbsp;  &nbsp; 隧道使用SSL等加密手段建立与其他服务器的通信线路，作用是保证安全通信，隧道本身不会解析HTTP请求，即原样转发。

<br>


## 二．	HTTP首部
### （一）	HHTP报文首部
&nbsp;  &nbsp;  &nbsp;  &nbsp; HTTP报文首部是必定包含在HTTP报文中的，如HTTP请求报文包含着请求行（方法，URI，HTTP版本），HTTP首部字段；HTTP响应报文包含着状态行（HTTP版本，状态码），HTTP首部字段。
<br>


### （二）	HTTP首部字段
#### 1.	首部字段作用
&nbsp;  &nbsp;  &nbsp;  &nbsp; HTTP首部字段的作用是传递额外信息，给客户端和服务器提供相关的信息，如报文的大小，使用的语言，认证信息等。
<br>


#### 2.	首部字段结构
&nbsp;  &nbsp;  &nbsp;  &nbsp; HTTP首部字段是由首部字段名和字段值构成的，中间用冒号：分割，单个字段名可以对应多个值。
<br>


#### 3.	首部字段类型
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通用首部字段：请求和响应都会使用的字段；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 请求首部字段：请求报文使用，补充了请求的附加内容，客户端信息等；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 响应首部字段：响应报文使用，补充了响应的附加内容；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 实体首部字段：请求和响应报文的实体部分使用的首部；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 非HTTP/1.1首部字段：其他首部字段以及自定义首部字段。
<br>


### （三）	通用首部字段
#### 1.	Cache-Control
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过指定该字段，就可以操作缓存，如no-cache表示客户端不会不缓存过期的资源，每次缓存前会向源服务器确认；no-store表示不能缓存请求或响应。
<br>


#### 2.	Connection
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段的作用有两个，一个是控制不再转发给代理的首部字段，在Connection字段中填入一个字段的名称，则不会转发该字段给下一个代理；另一个是管理持久连接，HTTP/1.1版本的默认连接都是持久连接，即Keep-alive，如果想要断开连接，则设置为close。
<br>


#### 3.	Date
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段表名创建HTTP报文的日期和时间。
<br>


#### 4.	Transfrer-Encoding
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段规定了传输报文主体时采用的编码方式，HTTP/1.1的传输编码方式仅对分块传输编码有效，即大文件分块传输，值为chunked。
<br>


#### 5.	Upgrade
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段用于检测HTTP协议及其他协议是否可以使用更高的版本进行通信。
<br>


#### 6.	Via
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段用于追踪请求和响应报文的传输路径，还可以避免请求回环的发生。在报文经过代理或网关时，会在Via中附加该服务器的信息然后再转发，这样下一个代理就可以得知是谁发送的报文。
<br>


### （四）	请求首部字段
#### 1.	Accept
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段可以通知服务器，用户代理可以处理的媒体类型以及媒体类型的优先级。使用q=来额外表示权重，1为最大值，默认是1.0。
<br>


#### 2.	Accept-Charset
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段用于通知服务器用户代理支持的字符集以及字符集的优先级。
<br>


#### 3.	Accept-Encoding
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段用来告知服务器用户代理支持的内容编码以及优先级。
<br>


#### 4.	Accept-Language
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段用来告知服务器用户代理能够处理的自然语言集以及优先级。
<br>


#### 5.	Authorization
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该资源用来告知服务器，用户代理的认证信息，想要通过服务器认证的用户代理会在接收到返回的401后，将Authorization字段加入请求中。
<br>


#### 6.	Expect
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知服务器，期望出现的某种特定行为。
<br>


#### 7.	From
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知服务器使用用户代理的用户的电子邮件地址。
<br>


#### 8.	Host
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知服务器请求的资源所处的主机名和端口号，Host首部是HTTP/1.1规范内唯一一个必须被包含在请求内的首部字段，这是因为一台服务器可能有多个虚拟主机，分配多个域名，因此需要准确地指定域名。
<br>


#### 9.	If-Match
&nbsp;  &nbsp;  &nbsp;  &nbsp; 形如If-XXX这样的字段，都是条件请求，服务器接受到请求后，只有判断条件为真，才会执行请求。
<br>


#### 10.	Max-Forwards
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段表示可经过服务器的最大数目。
<br>


#### 11.	Range
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段用于范围请求，指定资源的范围。
<br>


#### 12.	Referer
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段会告知服务器请求的原始资源的URI，客户端一般都会发送Referer字段给服务器，但是如果URI是在浏览器地址栏中输入的，处于安全性的考虑，可能包含账号密码等信息，也不会发送该首部字段。
<br>


#### 13.	User-Agent
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段会将创建请求的浏览器和用户代理名称等信息传递给服务器。

<br>


### （五）	响应首部字段
#### 1.	Accept-Ranges
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段用于告知客户端服务器能否处理范围请求，可处理时值为bytes,否则为none。
<br>


#### 2.	Age 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端，源服务器在多久前创建了相应的，单位为秒。
<br>


#### 3.	ETag
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端实体的标识，将资源以字符串形式做唯一性标识，为每一个资源分配一个ETag值，值是由服务器分配的，没有统一的算法规则。ETag值分为强ETag值和弱ETag值，一个即使是细微差别ETag也会变化，另一个只用于提示资源是否相同，只有资源发生了根本改变才会改变ETag值。
<br>


#### 4.	Location
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段将客户端引导至某个与请求URI不同的资源上，一般与3XX状态码配合使用，提供重定向的URI。
<br>


#### 5.	Retry-After
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端多久后再次发送请求。
<br>


#### 6.	Server
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端当前服务器上安装的HTTP服务器应用程序的信息。
<br>


### （六）	实体首部字段
#### 1.	Allow
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段通知客户端能够支持的HTTP方法，当服务器接受到不支持的HTTP方法时，会返回405状态码（Method Not Allowed），并将所有支持的方法写入Allow字段中返回。
<br>


#### 2.	Content-Encoding
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端服务器对实体的主体部分选用的内容编码方式，如gzip等。

<br>

#### 3.	Content-Language
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端，实体主体采用的自然语言。
<br>


#### 4.	Content-Length
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端实体主体部分的大小，单位是字节。
<br>


#### 5.	Content-Location
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段给出与报文主体部分对应的URI。
<br>


#### 6.	Content-MD5
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段由MD5算法生成的值组成，目的在于检查报文主体在传输过程中是否完整。对报文主体执行MD5算法得到的128位二进制数，由于HTTP首部字段无法记录二进制值，需要再通过Base64编码后将结果写入Content-MD5中，接收端会报文再次做MD5计算与Content-MD5比较得出结论。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 但是这种方式是不安全的，如果报文主体能够修改的话，将主体和对应的MD5都修改了， 接收端无法得知主体是否修改了。
<br>


#### 7.	Content-Range
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段告知客户端实体的哪个部分符合范围请求，字段值以字节为单位。
<br>


#### 8.	Content-Type
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段说明了主体内对象的媒体类型。

<br>

#### 9.	Expires
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段将资源失效的日期告知客户端。
<br>


#### 10.	Last-Modified
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该字段指明了资源的最终修改时间。
<br>


### （七）	Cookie对应的首部字段
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 管理服务器和客户端之间状态的Cookie的工作机制是用户识别和状态管理。浏览器会缓存用户的Cookie，在访问网站时取出Cookie携带访问。
<br>


#### 2.	Set-Cookie
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当服务器准备开始管理客户端的状态时，会事先告知各种信息，Set-Cookie字段的作用就是由服务器发送Cookie给客户端。
<br>


#### 3.	Cookie
&nbsp;  &nbsp;  &nbsp;  &nbsp; 客户端接受到服务器的Set-Cookie字段后，将其中的Cookie字段在下次请求时发送给服务器。





