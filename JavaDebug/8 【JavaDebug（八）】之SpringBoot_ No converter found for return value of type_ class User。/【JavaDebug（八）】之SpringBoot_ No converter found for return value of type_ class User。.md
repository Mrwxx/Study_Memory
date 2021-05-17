## 一．	BUG出现环境
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用SpringBoot搭建简单的Web项目时，定义了一个User类，在Controller中返回一个User对象，出现以下BUG：No converter found for return value of type: class User。
<br>


## 二.Bug解决
&nbsp;  &nbsp;  &nbsp;  &nbsp;根据英文，意思是没有返回User对象的转换器，感觉跟User类内部的定义有关，一般这种情况都可能是User类内部没有定义一些函数导致的。
查看User类，果然，没有定义get和set方法，添加上之后，问题解决。




