## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	JQuery
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery是一个快速，简洁的JavaScript框架，它封装了JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操作，事件处理，动画设计和Ajax交互。
<br>


### （二）	JQuery的使用
#### 1，	下载JQuery
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery一般有两个文件，一个是jquery-xxx.js，一个是jquery-xxx.min.js，两个js文件有什么区别呢？

&nbsp;  &nbsp;  &nbsp;  &nbsp;jquery-xxx.js是开发版本，有良好的缩进和注释，利于阅读，文件体积大一些。

&nbsp;  &nbsp;  &nbsp;  &nbsp;jquery-xxx.min.js是生产版本，程序中使用，没有缩进，体积小一些，程序能够更快地加载。
<br>



#### 2. 导入JQuery的js文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;导入min.js文件，即导入一个js文件：

```html
<script src=”js/jquery-3.3.1.min.js”></script>
```
<br>



### （三）	JQuery对象和JS对象的区别和转换
1.	JQuery对象和JS对象的方法是不通用的。

3.	JQuery对象要转换为JS对象，则用以下方式：


JQuery对象[索引]或者JQuery对象.get(索引)


3.	JS对象要转换为JQuery对象，则使用：

```javascript
$(JS对象)
```
<br>



### （四）	事件绑定与样式控制
#### 1.	事件绑定
&nbsp;  &nbsp;  &nbsp;  &nbsp;将click函数绑定到id为b1的元素上：

```javascript
$("#b1").click(function(){
	                alert("abc");
});
```
<br>



#### 2.	入口函数

```javascript
$(function () {
		           
});
```

window.onload和$(function)的区别：

&nbsp;  &nbsp;  &nbsp;  &nbsp;window.onload 只能定义一次,如果定义多次，后边的会将前边的覆盖掉。`$(function)`可以定义多次的。
<br>



#### 3.	样式控制
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过css方法进行样式控制：

```javascript
$(“#div”).css(“color”, “red”);
```
<br>



### （五）	选择器
#### 1.	基本选择器
##### （1）	标签选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取所有匹配标签名称的元素。

```javascript
$(“html标签名”)
```
	<br>



##### （2）	id选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取与指定id属性值匹配的元素
			

```javascript
$(“#id的属性值”)
```
<br>



##### （3）	class选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取与指定的class属性值匹配的元素
		

```javascript
$(“.class的属性值”)
```
<br>



##### （4）	并集选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取多个选择器选中的元素：

```javascript
$(“选择器1，， 选择器2…”)
```
<br>



#### 2.	层级选择器
##### （1）	后代选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;A和B之间用空格隔开，选择A元素的所有后代B元素，后代指的是子辈，孙辈…

```javascript
$(“A B”)
```
<br>



##### （2）	子选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;选择A元素内部的所有B子元素，仅仅是儿子辈的。

```javascript
$(“A> B”)
```
<br>



#### 3.	属性选择器
##### （1）	属性名称选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;选择包含指定属性的元素，如查找所有包含了属性B的A元素。

```javascript
$(“A[属性名B]”)
```
<br>



##### （2）	属性选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;选择包含指定属性等于指定值的元素，如查找所有属性名B=值C的元素A：

```javascript
$(“A[属性名B=”值C”]”)
```
<br>



##### （3）	复合属性选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;包含多个属性条件的选择器：

```javascript
$(“A[属性名=”值”][]…”)
```
<br>



##### （4）	正则方式选择
&nbsp;  &nbsp;  &nbsp;  &nbsp;选择以指定字符串开始的属性值，如选择title属性以”te”开始的div元素。

```javascript
$(“div[title^=’es’]”)
```
&nbsp;  &nbsp;  &nbsp;  &nbsp;选择属性值以指定字符串结尾的元素，如选择title属性以字符串”est”结尾的div元素：
		

```javascript
$(“div[title$=”est”]”)
```
	
&nbsp;  &nbsp;  &nbsp;  &nbsp;选择属性值包含指定字符串的元素，如选择title属性值包含”es”的div元素：
		

```javascript
$(“div[title*=”es”]”)
```
<br>



#### 4.	过滤选择器
##### （1）	首元素选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获得选择元素中的第一个元素：

```javascript
:first
```
<br>



##### （2）	尾元素选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获得选择的元素中的最后一个元素：

```javascript
:last
```
<br>



##### （3）	偶数选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;选择偶数，从0开始计数

```javascript
:even
```
<br>



##### （4）	奇数选择器

```javascript
:odd
```
<br>



##### （5）	等于索引选择器

```javascript
:eq(index)
```
<br>



##### （6）	大于索引选择器

```javascript
:gt(index)
```
<br>



##### （7）	小于索引选择器

```javascript
:lt(index)
```
<br>



##### （8）	标题选择器

```javascript
:header
```
<br>



#### 5.	表单过滤选择器
##### （1）	可用元素选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取可用的元素

```javascript
:enabled
```
<br>



##### （2）	不可用元素选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取不可用元素

```javascript
:disabled
```
<br>



##### （3）	选中选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获得单选/复选框中选中的元素：

```javascript
:checked
```
<br>



##### （4）	选中选择器
&nbsp;  &nbsp;  &nbsp;  &nbsp;获得下拉框中选中的元素：

```javascript
:selected
```
<br>



### （六）	DOM操作
#### 1.	内容操作
##### （1）	html()
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取或设置元素的标签体的内容，html()获取的是完整的标签体内容，而不是文本内容，如：

```html
<font>内容</font>
```

<br>


##### （2）	text()
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取或设置元素的标签体纯文本内容
<br>



##### （3）	val()
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取或设置元素的value属性值
<br>



#### 2.	属性操作
##### （1）	attr()
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取或设置元素的属性，多用于元素自定义的属性

获取属性值：

```javascript
$(“#b”).attr(“name”)
```

设置属性值：

```javascript
$(“#b”).attr(“name”, “exx”)
```
<br>



##### （2）	removeAttr()
删除属性

```javascript
$(“#b”).removeAttr(“name”)
```
<br>



##### （3）	prop()
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取或者设置元素的属性，多用于元素的固有属性。

```javascript
$(“#b”).prop(“checked”)
```
<br>



##### （4）	removeProp()
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除属性
<br>



#### 3.	操作class属性
##### （1）	addClass()
&nbsp;  &nbsp;  &nbsp;  &nbsp;添加class属性值
<br>



##### （2）	removeClass()
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除class属性值
<br>



##### （3）	toggleClass()
&nbsp;  &nbsp;  &nbsp;  &nbsp;切换class属性，若存在该属性值，则删除，弱不存在，则添加。如：

&nbsp;  &nbsp;  &nbsp;  &nbsp;`toggleClass(“one”)`，如果该元素对象上存在class=’one”，则删除one属性值，如果不存在class=”one”，则添加该属性值。
<br>



#### 4.	CRUD操作
##### （1）	append()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.append(对象2)`，将对象2添加到对象1元素的内部末尾、
<br>



##### （2）	prepend()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.prepend(对象2)`，将对象2添加到对象1的内部开头。
<br>



##### （3）	appendTo()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.appendTo()`，将对象1添加到对象2的内部末尾。
<br>



##### （4）	prependTo()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.perpendTo()`，将对象1添加到对象2内部的开头
<br>



##### （5）	after()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.after（对象2）`，将对象2添加到对象1后面，两者为兄弟关系。

<br>


##### （6）	before()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.before（对象2）`，将对象2添加到对象1前面，两者为兄弟关系。
<br>



##### （7）	insertAfter()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.insertAfter(对象2)`，将对象2添加到对象1后面，两者兄弟关系。

<br>


##### （8）	insertBefore()
&nbsp;  &nbsp;  &nbsp;  &nbsp;`对象1.insertBefore(对象2)`，将对象2添加到对象1的前面，两者兄弟关系。
<br>



##### （9）	remove()
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除对象
<br>



##### （10）	empty()
&nbsp;  &nbsp;  &nbsp;  &nbsp;清空对象的所有后代元素，但是保留当前对象以及其属性节点。
<br>




## 二．	JQuery动画
### （一）	显示和隐藏元素
#### 1.	show([speed], [easing], [fn])
&nbsp;  &nbsp;  &nbsp;  &nbsp;展示该元素。

参数介绍：

&nbsp;  &nbsp;  &nbsp;  &nbsp;speed:动画的速度，预定义值为slow, normal ,fast，或者传入动画时长的毫秒数值。

&nbsp;  &nbsp;  &nbsp;  &nbsp;easing:指定切换效果，默认是swing，可选linear。swing的效果是先慢，后快，再慢；linear的效果是匀速的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;fn:在动画完成时执行的函数，每个元素执行一次

```javascript
$(“#a”).show(“fast”, “linear”);
```
<br>



#### 2.	hide([speed], [easing], [fn])
&nbsp;  &nbsp;  &nbsp;  &nbsp;隐藏该元素。


```javascript
$(“#a”).hide(“fast”, “linear”);
```
<br>



#### 3.	toggle([speed], [easing], [fn])
&nbsp;  &nbsp;  &nbsp;  &nbsp;切换显示和隐藏元素。
<br>



### （二）	滑动显示和隐藏
#### 1.	slideDown(speed, easing,fn)
&nbsp;  &nbsp;  &nbsp;  &nbsp;滑动下拉显示元素。
<br>



#### 2.	slidUp(speed, easing, fn)
&nbsp;  &nbsp;  &nbsp;  &nbsp;滑动上升隐藏元素。
<br>



#### 3.	slideToggle(speed, easing, fn)
&nbsp;  &nbsp;  &nbsp;  &nbsp;切换滑动下拉与上升。
<br>



### （三）	淡入淡出显示和隐藏
#### 1.	fadeIn(speed, easing, fn)
&nbsp;  &nbsp;  &nbsp;  &nbsp;淡入效果
<br>



#### 2.	fadeout(speed, easing ,fn)
&nbsp;  &nbsp;  &nbsp;  &nbsp;淡出效果
<br>



#### 3.	fadeToggle(speed, easing ,fn)
&nbsp;  &nbsp;  &nbsp;  &nbsp;切换效果

<br>



## 三．	JQuery的遍历
### （一）	JS的遍历
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery中可以使用JS的遍历方式，，即通过for循环的方式。
<br>



### （二）	each(callback)方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery对象的each方法需要传入一个callback函数。
<br>



#### 1.	直接用this获取对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;this代表着遍历循环中的一个对象，该对象为一个JS对象，可以调用JS方法或者转换为JQuery对象调用JQuery方法。

```javascript
citys.each(function(){
		alert(this.innerHTML);
}
)
```
<br>



#### 2.	callback函数中传入参数
&nbsp;  &nbsp;  &nbsp;  &nbsp;回调函数中可以传入参数index, element，代表着遍历循环中的索引值和该索引对应的对象。

```javascript
citys.each(function(index, element){
alert(index + “:” + element.innerHTML);
}
)
```
<br>



#### 3.	循环的结束
&nbsp;  &nbsp;  &nbsp;  &nbsp;如果function函数返回的是false，则结束循环（break）；

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果function函数返回的是true，则结束本次循环，继续下次循环（continue）
<br>



### （三）$.each(obj, callback)
&nbsp;  &nbsp;  &nbsp;  &nbsp;obj为要遍历的数组，callback与之前一样是回调函数。

```javascript
$.each(citys, function(){
	alert($(this).html());
});
```
<br>



### （四）xx of xx
&nbsp;  &nbsp;  &nbsp;  &nbsp;与增强for循环的用法一样。

```javascript
for(li of citys){
	alert($(li).html());
}
```
<br>



## 四．	事件的绑定
### （一）	JQuery标准的绑定方式
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery对象.事件方法（回调函数）;

```javascript
$(“#name”).click(function(){
	alert(“click”);
})
```

<br>


### （二）	on绑定事件/off解绑
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery对象.on(“事件名称”, 回调函数);

&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery对象.off(“事件名称”, 回调函数);

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果off方法不传递任何参数，则将组建上的所有事件全部解绑

```javascript
$(“#tag”).on(“click”, function(){
	alert(“click”);
})
```
<br>



### （三）	toggle事件切换
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery对象.toggle(fn1, fn2…)

&nbsp;  &nbsp;  &nbsp;  &nbsp;当当前的JQuery对象对应的组件后，会执行fn1，第二次点击会执行fn2

```javascript
$(“#btn”).toggle(function(){
		$(“#div”).css(“color”, “red”);
}, function(){
		alert(“color”);
});
```
<br>




## 五．	JQuery的插件
&nbsp;  &nbsp;  &nbsp;  &nbsp;JQuery允许我们自定义插件来增强JQuery的功能，即增加JQuery的方法。

### 1.	$.fn.extend(object)
&nbsp;  &nbsp;  &nbsp;  &nbsp;增强JQuery获取的对象的功能，即为JQuery选中的元素添加方法。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如：为所有的JQuery对象添加check()方法：

```javascript
$.fn.extend({
	check:function(){
		this.prop(“checked”, true);
	}
})
```
<br>



#### 2.	$.extend(object)
&nbsp;  &nbsp;  &nbsp;  &nbsp;增强JQuery对象自身的功能,可以不用通过获取JQuery对象来调用，可以直接通过$调用。即添加全局方法。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如，添加全局方法max，求最大值：

```javascript
$.extend({
		max:function(a, b){
			return a >= b ? a : b;
		}
})
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;调用max方法的话直接通过$.max（）调用即可。











