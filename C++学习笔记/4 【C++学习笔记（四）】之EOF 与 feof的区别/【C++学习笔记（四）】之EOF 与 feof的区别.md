## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

#### EOF

 &nbsp;  &nbsp;  &nbsp;  &nbsp;EOF是一个整数类型的宏定义，扩展为负数常量表达式（通常为-1）。它被头文件<cstdio>中的好几个函数作为返回值使用，来表明已经到达文件尾或者示意一些其他的错误条件。它也被作为一个值来表示无效的字符。
#### feof

 &nbsp;  &nbsp;  &nbsp;  &nbsp;feof用于判断文件结尾，存在于头文件<cstdio.h>。使用方法是feof(fp)，fp为指向需要判断的文件的指针。如果不到文件结尾，返回0值；如果是文件结尾，返回非0.
###### 注意：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;调用feof函数时，指针只有超过文件尾读取数据，才会发生异常。使用feof函数的逻辑是先读再判断。

#### EOF 和 feof的区别：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;EOF（-1）通常用来判断文本文件的结尾，因为ASCII码值没有-1 的情况。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;二进制文件中，存放的是数值，可能有-1，因此不能用EOF来判断，要用feof函数。


