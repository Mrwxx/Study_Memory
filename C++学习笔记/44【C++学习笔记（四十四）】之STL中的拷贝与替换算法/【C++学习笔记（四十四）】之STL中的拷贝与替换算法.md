## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Copy算法
 &nbsp;  &nbsp;  &nbsp;  &nbsp;将容器中指定范围的元素拷贝到另一容器中，另一容器需要手动分配内存。
 <br>

	@param beg 容器开始迭代器
	@param end 容器结束迭代器
	@param dest 目标起始迭代器
 <br>
 
## 二．	Replace算法
 &nbsp;  &nbsp;  &nbsp;  &nbsp;将容器中指定范围的旧元素改为新元素
 <br>
 
	@param beg 容器开始迭代器
	@param end 容器结束迭代器
	@param oldvalue 旧元素
	@param oldvalue 新元素
 <br>
 
## 三．	Replace_if算法
 &nbsp;  &nbsp;  &nbsp;  &nbsp;同样是替换元素，但是可以用回调函数或者谓词（返回bool的函数对象）来设置条件
 <br>
 
	@param beg 容器开始迭代器
	@param end 容器结束迭代器
	@param callback函数回调或者谓词(返回Bool类型的函数对象)
	@param oldvalue 新元素
 <br>
 
## 四．	Swap算法
 &nbsp;  &nbsp;  &nbsp;  &nbsp;互换两个容器的元素

 <br>
 
	@param c1容器1
	@param c2容器2


