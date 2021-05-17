## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	二维离散傅里叶变换

&nbsp;  &nbsp;  &nbsp;  &nbsp; 令f(x, y)表示一幅大小为M x N的图像，其中x=0,1,2... M- 1和y=0,1,2,-,N-1。f的二维离散傅里叶变换可表示为F(u, v),如下式所示:

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720155421155.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 其中u=0, 1,2, ... M- 1和v=0, 1,2, …. N- 1。我们可以将指数项扩展为正弦项和余弦项的形式,其中变量u和v用于确定它们的频率。

**

> &nbsp;  &nbsp;  &nbsp;  &nbsp; 频域系统是由F (u,
> v)所张成的坐标系，其中u和v用做(频率)变量。空间域是由f(x, y)所张成的坐标系，其中x和y用做(空间)变量。

**

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由u=0,1,2, ...,M- 1和v=0,1,2....,N- 1定义的**M x N矩形区域常称为频率矩形**。显然，频率矩形的大小与输人图像的大小相同。


## 二.	离散傅里叶逆变换

&nbsp;  &nbsp;  &nbsp;  &nbsp; 离散傅里叶逆变换由下式给出：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720155522367.png)

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 其中x=0,1,2...,M- 1和y=0,12...,N-1。因此,给定F(u, v),我们就可借助于逆DFT得到f(x, y)**。在这个等式中，F (u, v)的值有时称为傅里叶系数。

<br>

## 三.	分析变换
### (一)频谱
&nbsp;  &nbsp;  &nbsp;  &nbsp; 直观地分析一个变换的方法是计算它的频谱，即F(u,v)的幅度，并显示为图像。

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 频域原点处变换的值（F(0,0)）称为傅里叶变换的直流（dc）分量，该术语源于直流电（频率为0的电流）。可以看出，F(0,0)等于f(x,y)的平均值的MN倍。**

&nbsp;  &nbsp;  &nbsp;  &nbsp; 即使f(x,y)为实数，其傅里叶变换也通常是复数。令R(u,v)和I(u,v)分别表示F(u,v)的实部和虚部，则傅里叶频谱定义为：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720155650353.png)

 
### （二）相位角
<br>

变换的相位角定义为：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072015570475.png)
<br>
**则F(u,v)可表示为：**
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720155709421.png)
<br>


## 四．DFT的周期性
&nbsp;  &nbsp;  &nbsp;  &nbsp; DFT在u和v的方向上都是周期无穷的，周期由M和N决定。
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720155756307.png)


**&nbsp;  &nbsp;  &nbsp;  &nbsp; 傅里叶逆变换得到的图像也是周期无穷的。**
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720155802175.png)

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 因为DFT是周期无穷的，那么要实现DFT就只需要计算一个周期即可，所以我们处理的数组实际上是M x N的数组。**

**&nbsp;  &nbsp;  &nbsp;  &nbsp; 同时，在二维DFT中，为了简化频谱的视觉分析，可以将原点的变换值移动到频率矩形的中心位置。这可以通过再计算二维傅里叶变换之前将f(x,y)乘以(-1)^(x+y)^来完成。**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720155808779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)


**&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，a图是原始的DFT变换，b图是移动了原点后的DFT图，a图中的(-M/2,-N/2)处的频谱值域b图中的(0,0)处的频谱值相等，即原点被移动了。**
<br>

## 五．DFT的MATLAB实现
### (一)快速傅里叶变换FFT
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在实际应用中，DFT及其逆变换可以通过使用快速傅里叶变换( FFT)算法来实现。一个大小为M x N的图像数组f可以通过工具箱中的**函数fft2**得到，函数fft2的简单语法为

**&nbsp;  &nbsp;  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp;  &nbsp; F = ff2(f)**

&nbsp;  &nbsp;  &nbsp;  &nbsp; 该函数返回一个大小仍为M x N的傅里叶变换;数据的原点在左上角，而四个四分之一周期交汇于频率矩形的中心。

### （二）傅里叶频谱
&nbsp;  &nbsp;  &nbsp;  &nbsp; 傅里叶频谱可以使用函数abs来获得:

**&nbsp;  &nbsp;  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp;  &nbsp; S = abs(F) .**

&nbsp;  &nbsp;  &nbsp;  &nbsp; 该函数计算数组的每一个元素的幅度(实部和虚部平方和的平方根)。.

### （三）原点移动
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们可以使用 fftshift函数将变换的原点移动到频率矩形的中心：

&nbsp;  &nbsp;  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp;  &nbsp; **Fc = fftshift(F)**

&nbsp;  &nbsp;  &nbsp;  &nbsp; F是 fft2计算得到的变换，Fc是已居中的变换。

### （四）视觉增强

&nbsp;  &nbsp;  &nbsp;  &nbsp; 虽然该移动像我们期望的那样完成了，但该频谱中值的动态范围(0到204 000)与8比特显示(此时中心处的明亮值占支配地位)相比要大得多。**我们可以使用对数变换来处理该问题**。对数变换后，视觉效果明显增强。

 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200720160202859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)


