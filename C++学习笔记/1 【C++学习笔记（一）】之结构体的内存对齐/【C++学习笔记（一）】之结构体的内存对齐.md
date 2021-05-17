## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

#### 一. 结构体的内存对齐：
&nbsp;  &nbsp;  &nbsp;  &nbsp; 结构体可以存储不同类型的数据，每种数据所占的字节数都不同。而结构体是按照最大字节数来存储的，若结构体中最大的数据类型占8个字节，则结构体存储的内存中按8个字节为一格存储，将其他较少字节数的数据往里填，若有空余，则看下一个数据能否填入；若不能，则按照内存对齐原则，从下一格重新开始填入数据，空余的内存则被跳过。
#### 二. 设置结构体如下：
```cpp
struct stus{
	char *p;			//4
	char arr[2];		//1*2
	int c;				//4
	short d;			//2
	double f;			//8
	long g;				//4
	float h[2];			//4*2
};
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; **若不调整结构体中数据存储顺序，则按最大数据类型8个字节为一格，依次填入，如下图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200607102452874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
&nbsp;  &nbsp;  &nbsp;  &nbsp; **红色圈为空余的内存**
&nbsp;  &nbsp;  &nbsp;  &nbsp; **计算得到结构体整体内存占40个字节**

**若将结构体中数据类型从大到小排列:**

```cpp
struct stus{
	double f;			//8
	float h[2];			//4*2
	long g;				//4
	int c;				//4
	char *p;			//4
	short d;			//2
	char arr[2];		//1*2
};
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; **调整后结构体整体内存占32个字节**
#### 结论：
&nbsp;  &nbsp;  &nbsp;  &nbsp; 内存空余的情况得到了优化。但是打乱结构体的排列顺序也可能导致阅读结构体的思维混乱，所以需要平衡这两者。


