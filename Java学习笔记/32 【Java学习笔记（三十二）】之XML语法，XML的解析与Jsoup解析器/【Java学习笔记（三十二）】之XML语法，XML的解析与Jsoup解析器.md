## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	XML
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Extensible Markup Language，可扩展标记语言。所谓的可扩展性指的是XML的标签都是自定义的，不像HTML中标签都是预定义的。
<br>


### （二）	功能
&nbsp;  &nbsp;  &nbsp;  &nbsp;XML是用于存储数据的，一般用于配置文件和网络中传输。
<br>



### （三）	XML与HTML的区别
1.	XML标签是自定义的，HTML标签是预定义的

2.	XML的语法严格，HTML的语法松散

4.	XML用于存储数据，HTML用于展示页面

<br>



## 二．	XML的语法
### （一）	基本语法
1.	XML文档中有且仅有一个根标签

2.	XML文档第一行为XML文档声明

3.	XML文档的后缀名为.xml

4.	XML文档的标签属性名需用引号引起来

6.	XML标签区分大小写
<br>



### （二）	XML文档的组成
#### 1.	文档声明
##### （1）	格式

```html
<?xml 属性列表 ？>
```
<br>



##### （2）	属性列表
**version:**  版本号，必写

**encoding:**  编码方式，告知解析引擎当前文档使用的字符集

**standalone:**：是否独立，独立即不依赖其他文件
<br>



#### 2.	标签
自定义的标签命名规则：
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	名称不能以数字或者标点符号开始	

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	名称可以包含字母，数字以及其他字符

&nbsp;  &nbsp;  &nbsp;  &nbsp;（3）	名称不能以xml（XML）开始
<br>



#### 3.	指令
&nbsp;  &nbsp;  &nbsp;  &nbsp;结合CSS控制样式
<br>



#### 4.	文本
&nbsp;  &nbsp;  &nbsp;  &nbsp;CDATA区：能够将该区域中的数据原样展示出来，如：

```html
<![CDATA[数据]]>
```
<br>



### （三）	XML文档的约束
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;XML文档受到约束文档的限制，通过约束文档来规范XML文档的书写规则。将约束文档引入XML文档中。
<br>



#### 2.	约束的分类
##### （1）	DTD
&nbsp;  &nbsp;  &nbsp;  &nbsp;dtd文档的简单语法：

通过Element来定义标签
&nbsp;  &nbsp;  &nbsp;  &nbsp;如：定义了一个students标签，该标签中有多个student标签。

```html
<!ELEMENT students(student*)>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;如：定义一个student标签，该标签还有 name，age，sex三个标签，每个标签只能按照顺序出现一次。

```html
<!ELEMENT student(name, age, sex)>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;如：定义一个name标签，该标签中存储的是字符串

```html
<!ELEMENT name(#PCDATA)>
```

<br>



将dtd文档引入到XML文档中，分为两种方式：
&nbsp;  &nbsp;  &nbsp;  &nbsp;一种是写在XML文档内部的dtd

&nbsp;  &nbsp;  &nbsp;  &nbsp;一种是写在XML文档外部的dtd文档。
&nbsp;  &nbsp;  &nbsp;  &nbsp;外部的dtd文档又分为本地文档与网络中的文档：

本地dtd：`<!DOCTYPE 根标签名SYSTEM “dtd文件的位置”>`

&nbsp;  &nbsp;  &nbsp;  &nbsp;根标签名为该dtd文档约束的XML文档中的根标签名，即最外层的标签名

&nbsp;  &nbsp;  &nbsp;  &nbsp;SYSTEM代表的是本地的dtd文档
<br>



网络dtd：`<!DOCTYPE 根标签名 PUBLIC “dtd文件名字” “dtd文件的位置URL”>`

&nbsp;  &nbsp;  &nbsp;  &nbsp;同理，PUBLIC 代表着网络中的dtd文件
<br>



##### （2）	Schema(后缀为xsd)


&nbsp;  &nbsp;  &nbsp;  &nbsp;xsd文档能够更加详细地限定XML的标签中的内容，当然也是更加的复杂。本质上xsd文档还是xml文档。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如：定义一个标签元素 students，类型是自定义类型studentsType

```html
<xsd:element name=”students” type=”studentsType” />
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;如：为students标签定义自定义类型studentsType，该类型中限定按照顺序存储student标签，student标签又限定为自定义studentType类型，最小值为0，最大值未限定。

```html
<xsd::complexType name=”studentsType”>
<xsd:sequence>
<xsd:element name=”student” type=”studentType” minOccurs=”0” maxOccurs=”unbounded” />
</xsd:sequence>
</xsd:complexType>
```
<br>



 

&nbsp;  &nbsp;  &nbsp;  &nbsp;接下来，为student定义studentType类型(复杂类型)，按照顺序存储name标签（类型为string），age标签(类型为ageType)，sex标签(类型为sexType)，最后限定一个必选的属性number，类型为自定义numberType。

```html
<xsd:complexType name=”studentType”>
<xsd:sequence>
<xsd:element name=”name” type=”xsd:string” />
<xsd:element name=”age” type=”ageType” />
<xsd:element name=”sex” type=”sexType” />
</xsd:sequence>
</xsd:attribute name=”number” type=”numberType” use=”required” />
</xsd:complexType>
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;以ageType举例，ageType为简单类型，该类型限定基本数据类型为integer，最小值为0，最大值为256.

```html
<xsd:simpleType name=”sexType”>
<xsd:restriction base=”xsd:integer”>
<xsd:minInclusive value=”0” />
<xsd:maxInclusive value=”256”/>
</xsd:restriction>
</xsd:simpleType>
```

<br>



xsd文档的导入：

1.	填写XML文档的根标签students

2.	引入xsi前缀，xmlns:xsi=””

3.	引入xsd文件命名空间 xsi:schemaLocation=””

5.	为每一个xsd约束空间声明一个前缀，作为标识，因为当多个xsd文件中有相同的标签名需要区别，使用前缀会更加的方便。如：将该xsd声明为a ，xmlns:a=””

如：

```html
<students xmlns:xsi=http://www.w3.org/2001/XMLSchema-instance
xsi:schemaLocation=”./xml student.xsd”
xmlns:a=”./xml”>
```
<br>



## 三．	XML的解析
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;操作XML文档，将文档中的数据读取到内存中，或者将内存中的数据保存到XML文档中，持久化地存储。
<br>



### （二）	解析XML的方式
#### 1.	DOM
&nbsp;  &nbsp;  &nbsp;  &nbsp;将标记语言文档一次性地加载到内存中，在内存中形成一个DOM树。
<br>



##### （1）	优点
&nbsp;  &nbsp;  &nbsp;  &nbsp;操作非常方便，可以对文档进行增删改查的操作
<br>



##### （2）	缺点
&nbsp;  &nbsp;  &nbsp;  &nbsp;缺点是DOM树是一开始就会生成的，且非常占内存，因此一般用于服务器端。
<br>



#### 2.	SAX
&nbsp;  &nbsp;  &nbsp;  &nbsp;不是一次性地加载到内存中，而是逐行读取。
<br>



##### （1）	优点
&nbsp;  &nbsp;  &nbsp;  &nbsp;不会占用太大的内存，因此经常用于移动端等内存不大的环境中。
<br>



##### （2）	缺点
&nbsp;  &nbsp;  &nbsp;  &nbsp;只能读取，不能增删改操作。
<br>



#### 3.	XML的常见解析器
##### （1）	JAXP
&nbsp;  &nbsp;  &nbsp;  &nbsp;sun公司提供的解析器，支持DOM和SAX两种思想
<br>



##### （2）	DOM4J

##### （3）	Jsoup

&nbsp;  &nbsp;  &nbsp;  &nbsp;Java的一款HTML解析器，可直接解析某个URL地址，HTML文本内容，它提供了一套省力的API，可通过DOM，CSS以及类似于JQuery的操作方法来取出和操作数据。

<br>



## 四．	Jsoup解析器
### （一）	Jsoup的使用步骤
1.	导入Jsoup的jar包

2.	获取Document对象

3.	获取对应的标签Element对象

5.	获取数据
<br>



### （二）	对象的使用
#### 1.	Jsoup
&nbsp;  &nbsp;  &nbsp;  &nbsp;工具类，可以解析HTML或XML文档，返回Document对象

`Jsoup.parse(File in, String charsetName)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;解析xml或HTML文件

`Jsoup.parse(String html)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;解析html或xml字符串

`Jsoup.parse(URL url, int timeoutMillis)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过网络路径获取指定的HTML或xml的文档对象

<br>


#### 2.	Document
&nbsp;  &nbsp;  &nbsp;  &nbsp;文档对象，代表内存中的DOM树，获取Element对象的方法如下所示：

`getElementById(String id)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据id属性值获取唯一的element对象

`getElementsByTag(String tagName)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据标签名称获取元素对象集合

`getElementsByAttribute(String key)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据属性名称获取元素对象集合

`getElementsByAttributeValue(String key, String value)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据对应的属性名和属性值获取元素对象集合
<br>



#### 3.	Elements
&nbsp;  &nbsp;  &nbsp;  &nbsp;Element对象的集合，可当做`ArrayList<Element>`来使用
<br>



#### 4.	Element
&nbsp;  &nbsp;  &nbsp;  &nbsp;元素对象，在该元素对象中使用如下方法获取子元素对象：

`getElementById(String id)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据id属性值获取唯一的element对象

`getElementsByTag(String tagName)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据标签名称获取元素对象集合

`getElementsByAttribute(String key)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据属性名称获取元素对象集合

`getElementsByAttributeValue(String key, String value)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据对应的属性名和属性值获取

<br>



在该元素对象中使用如下方法获取属性值：

`String attr(String key)`
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据属性名称获取属性值

&nbsp;  &nbsp;  &nbsp;  &nbsp;在该元素对象中使用如下方法获取文本内容：
`String text()`
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取子元素对象中的所有纯文本内容

`String html()`
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取子元素对象中的所有内容（包括子元素的标签和纯文本内容）

<br>




### （三）	Jsoup选择器查询
#### 1.	selector:选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过选择器可以查找出文档中的标签信息，属性信息以及文本信息。

```html
Elements select(String cssQuery)
```

如：
&nbsp;  &nbsp;  &nbsp;  &nbsp;查找document中标签名为name的元素

```html
document.select(“name”);
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查找document中id值为a的元素

```html
document.select(“#a”);
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查找doocument中的student标签，且该标签的number属性值为a的子标签

```html
document.select(“student[number=\”a\”]”);
```
<br>



#### 2.	Xpath
&nbsp;  &nbsp;  &nbsp;  &nbsp;Xpath为XML的路径语言，是一种用来确定XML文档中某部分位置的语言，使用Jsoup的Xpath需要额外导入Xpath的jar包。通过查询手册，使用Xpath的语法查询。

如：
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过标签名查询，标签名前是使用//

```html
List<JXNode> jxNodes = jxDocument.selN(“//student”);
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;查询student标签下的子标签name:，子标签中间用/隔开

```html
List<JXNode> jxNodes = jxDocument.selN(“//student/name”);
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查询student标签下id属性等于a的name标签，使用@表示id

```html
LIst<JXNode> jxNode = jxDocument.selN(“//student/name[@id=’a’]”);
```






