## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	支持向量机的作用
**目的**：做分类，并且分类的边界越宽越好

**何谓边界的宽度呢**？请看下图：
&nbsp;  &nbsp;  &nbsp;  &nbsp;大家可以看到，左图的边界宽度明显比右图的宽度要小很多，**边界越宽当然越好，即分类的泛性越大**，即若有更多的点需要分类，那么右图的分类边界表现一般是更好的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714190506412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
## 二.	推导边界点到分类平面的距离公式
&nbsp;  &nbsp;  &nbsp;  &nbsp;要得到最宽的分类边界，跟最靠近分类边界的点有关，因为分类的边界本就是为了分割开不同种类的点，而最靠近边界的点就在边界的最边上。 

**&nbsp;  &nbsp;  &nbsp;  &nbsp;那么，既然说最靠近边界的点与边界的宽度有关，如何求最靠近边界的点与边界的距离呢**？

### （一）利用平面向量与法向量
&nbsp;  &nbsp;  &nbsp;  &nbsp;这里，我们可以把边界想象成一个平面，那么要求分类的点与边界的距离，就是求点到平面的距离。点到平面的距离是如何定义的呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp;这里来推导一下公式。
#### 1.构造平面公式
**&nbsp;  &nbsp;  &nbsp;  &nbsp;假设我们要求的点为x, 假设边界为平面，平面的一般公式大家应该都知道，即 y = w^T^x + b， w^T^为平面的法向量，b为偏置量。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;在平面上假设有两个点 x1, x2, 将两个点代入平面的公式中，即
&nbsp;  &nbsp;  &nbsp;  &nbsp;w^T^x1 + b = 0
&nbsp;  &nbsp;  &nbsp;  &nbsp;w^T^x2 +b = 0
**&nbsp;  &nbsp;  &nbsp;  &nbsp;同时，两个点组成了平面上的一个向量，而平面的法向量wT是垂直于平面上的向量的**，因此有：
&nbsp;  &nbsp;  &nbsp;  &nbsp;(w^T^, (x2 – x1)) = 0

#### 2.用平面向量与法向量求解投影
&nbsp;  &nbsp;  &nbsp;  &nbsp;有了平面上的一个向量(x2 – x1)，因此，我们要求的点x到平面的距离就可以配合着这个向量(x2-x1)来求，连接点x与点X1，构造一个向量(x-x1)，通过向量(x-x1)在平面的法向量上的投影即可求得点X到边界平面的距离。
**&nbsp;  &nbsp;  &nbsp;  &nbsp;根据向量的乘积公式可以得到向量(x-x1)与w^T^的乘积为**
&nbsp;  &nbsp;  &nbsp;  &nbsp;w^T^(x-x1) = ||w^T^|| ||x-x1|| cosθ
&nbsp;  &nbsp;  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp;  &nbsp;θ为两个向量的夹角<br>
&nbsp;  &nbsp;  &nbsp;  &nbsp;因为我们要求的就是向量(x-x1)在法向量上的投影距离，即||x-x1||cosθ，
&nbsp;  &nbsp;  &nbsp;  &nbsp;那么将右式中的||w^T^||除过去即可**得到。同时，考虑到向量乘积可能有负值，为数值添加绝对值符号，即可以求得点X到边界平面的距离为：**
&nbsp;  &nbsp;  &nbsp;  &nbsp;distance = |w^T^(x-x1)/||w|||
**&nbsp;  &nbsp;  &nbsp;  &nbsp;将x1, x2两点的平面式子w^T^x1 = -b, w^T^x2 = -b代入，可得**
&nbsp;  &nbsp;  &nbsp;  &nbsp;distance = |w^T^x+b| / ||w||

### （二）去掉绝对值
**&nbsp;  &nbsp;  &nbsp;  &nbsp;支持向量机要优化的目标是：
&nbsp;  &nbsp;  &nbsp;  &nbsp;每种边界都有一个离边界最近的点，我们要做的优化就是找出哪个边界的最近点离边界的距离是最大的，那么该边界就是我们要求的边界。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;边界的方程为 y = w^T^x + b
&nbsp;  &nbsp;  &nbsp;  &nbsp;那么，有了优化的目标，我们就要朝着这个目标来构造数学公式了。
&nbsp;  &nbsp;  &nbsp;  &nbsp;上面已经求出

&nbsp;  &nbsp;  &nbsp;  &nbsp;distance = |w^T^x+b| / ||w||

**&nbsp;  &nbsp;  &nbsp;  &nbsp;绝对值比较影响我们的求解，要想办法将绝对值去掉。去掉绝对值，即将负数变为正数，正数依然是正数，那么我给负数乘个-1，给正数乘个+1，不就把绝对值去掉了么！**
&nbsp;  &nbsp;  &nbsp;  &nbsp;而-1和+1正好和负例与正例相对应，那么我就将数据集的样本标签yi正例设为+1， 负例设为-1。同时，设置决策分类的结果，
&nbsp;  &nbsp;  &nbsp;  &nbsp;设置决策结果y(xi)为>0时为正例，正好对应了数据集的正例标签为+1；
&nbsp;  &nbsp;  &nbsp;  &nbsp;设置决策结果y(xi)为<0时为负例，正好对应了数据集的负例标签为-1；
**&nbsp;  &nbsp;  &nbsp;  &nbsp;因此，给distance的分子|w^Txi+b|（即y(xi)）乘以yi，即 yi y(xi) > 0，这样就将绝对值给去掉了。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;那么现在化简的distance式子为
&nbsp;  &nbsp;  &nbsp;  &nbsp;distance = y(w^T^xi+b) / ||w||

## 三.	目标函数
### （一）设置目标函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;去掉了绝对值，就要来设置我们的目标函数了，根据我们之前提出的优化目标，可以写出相应的数学式子：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714190851741.png)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;理解：大括号里面求的是每个分类边界的最近点到边界的距离，大括号外面是求什么样的w, b能够使得某个分类边界的最近点到边界的距离最大。**

### （二）简化目标函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;这个目标式子过于复杂，我们要想办法简化，因为 

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;[y (w^T^xi+b)] > 0

&nbsp;  &nbsp;  &nbsp;  &nbsp;我们可以通过放缩，使得

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;[y(w^T^xi+b)] >=1

&nbsp;  &nbsp;  &nbsp;  &nbsp;这样，优化目标式子中大括号里面的

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp; min[yi(w^T^xi+b)]就简化成了1

&nbsp;  &nbsp;  &nbsp;  &nbsp;因此目标函数简化为：

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;Argmax(w,b)  1 / ||w||

**&nbsp;  &nbsp;  &nbsp;  &nbsp;目标函数为求解极大值问题，但是，我们一般求解的都是极小值问题，因此，将求解1/||w||的极大值问题转换为求解||w||极小值问题。即**

&nbsp;  &nbsp;  &nbsp;  &nbsp;&nbsp;  &nbsp;  &nbsp;  &nbsp;Min(w,b） ||w||

&nbsp;  &nbsp;  &nbsp;  &nbsp;且前面假设了 yi(w^T^xi+b) >= 1这个条件

&nbsp;  &nbsp;  &nbsp;  &nbsp;同时考虑到之后要求导，且求解 ||w||的极小值问题与求解||w^2^||的极小值问题是一样的。则把目标函数变化为：

&nbsp;  &nbsp;  &nbsp;  &nbsp;min w,b 1/2 w^2^， 条件为 yi(w^T^xi+b) >= 1

### （三）求解目标函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;现在就要来求解这个方程了，一看这个问题，是不是很熟悉，考研的同学应该比较熟，用拉格朗日乘子法来求解。
#### 1.	拉格朗日乘子法
&nbsp;  &nbsp;  &nbsp;  &nbsp;拉格朗日乘子法用于求解极值问题，如下所示，求解f~0~(x)的极小值，下面是约束条件，可以为不等式或者等式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191049143.png)

**这是拉格朗日乘子法的标准式子：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191102318.png)

**这是将目标函数和约束条件带入拉格朗日乘子法公式：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191117166.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191123841.png)
**注意，多了参数 α**

&nbsp;  &nbsp;  &nbsp;  &nbsp;由于直接求解目标函数比较困难，通过拉格朗日乘子法求解，将求解w,b与中间参数 α 联系起来，那么通过求解 α的解，然后通过α与 w,b的关系，那么w,b就可以求解了。


**&nbsp;  &nbsp;  &nbsp;  &nbsp;拉格朗日乘子法， 求解方法：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191211792.png)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;对拉格朗日乘子法的式子先求最大值，再求极小值等价于对它先求极小值，再求极大值。**

#### 2.拉格朗日乘子法先求极小值
&nbsp;  &nbsp;  &nbsp;  &nbsp;因此，我们就先求L的极小值，即求解什么样的w,b能够使得L最小。
&nbsp;  &nbsp;  &nbsp;  &nbsp;求解极小值，就对L求偏导，有几个参数，就对几个参数分别求偏导，
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191233338.png)

**&nbsp;  &nbsp;  &nbsp;  &nbsp;将求出的等式带入原式中**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191250656.png)

**&nbsp;  &nbsp;  &nbsp;  &nbsp;将前面的对w,b求偏导得出的等式带入**

![](https://img-blog.csdnimg.cn/20200714191300719.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191321600.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;到此，我们完成了拉格朗日乘子法的第一步，求解** ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191339134.png)
#### 3.拉格朗日乘子法再求极大值
**&nbsp;  &nbsp;  &nbsp;  &nbsp;接下来求解第二步，求解什么样的 α 使得 上式结果最大**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191406747.png)
限制条件：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191426585.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191433311.png)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;第一个限制条件是之前对w求偏导得出的。
&nbsp;  &nbsp;  &nbsp;  &nbsp;第二个限制条件是拉格朗日乘子法规定的 参数 二发 必须 大于等于 0**

&nbsp;  &nbsp;  &nbsp;  &nbsp;又是求解极大值问题，将极大值转变为求解极小值问题，即添个负号即可转变：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191449935.png)
限制条件不变：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191505255.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191511913.png)
&nbsp;  &nbsp;  &nbsp;  &nbsp; **通过求解极小值，求出了 α，就可以求出w， 求出了w，就可以通过 y = w^T^x + b的公式求出b**
&nbsp;  &nbsp;  &nbsp;  &nbsp; **到此， 分类边界需要的参数都已经求出来了，即分类边界已求出！！！**


## 四．支持向量机名字由来
  
&nbsp;  &nbsp;  &nbsp;  &nbsp;讲到这里，大家对支持向量机这个名称是不是又疑问？明明是一个分类边界的算法为什么叫做支持向量机？
我们可以从w的公式中看出：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191529259.png)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;它与 α， y， x的值有关，可以知道x和y的值不会为0，而 α的值可能为0.当 α的值为0时，那么该点就与分类边界的形成毫无关系，就不是支持分类边界生成的支持向量了。那些 α 不为 0的点都是支持分类边界生成的支持向量，同时，这些点就是边界点，正式边界点对分类边界产生影响。这就是支持向量机这个名称的由来。**

## 五．软间隔
&nbsp;  &nbsp;  &nbsp;  &nbsp;软间隔：数据中有一些噪音点会造成严格的分类，导致更差的分类边界生成。
&nbsp;  &nbsp;  &nbsp;  &nbsp;因此，为了获得最好的分类边界，可以设置分类没有那么严格，可以容忍一些错误分类。
**&nbsp;  &nbsp;  &nbsp;  &nbsp;引入 松弛因子，使得**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191623415.png)
**那么，目标函数也会因此有所变化：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200714191636507.png)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;引入了控制参数C：（C是我们自己指定的参数）**
&nbsp;  &nbsp;  &nbsp;  &nbsp;当C值很大时，为了使目标函数最小，因此松弛因子就要更小，意味着分类更加严格。
&nbsp;  &nbsp;  &nbsp;  &nbsp;当C值很小时，乘以一个稍微大一点的数也没有影响。因此松弛因此可以适当放大，意味着可以容忍一些错误。

&nbsp;  &nbsp;  &nbsp;  &nbsp;同样，使用拉格朗日乘子法来求解。
## 六．核变换

&nbsp;  &nbsp;  &nbsp;  &nbsp;在低维度的问题中，有些问题用线性分类是无法分类的，那么我们可以将该问题转换到高维度上进行分类。这就叫做核变换。
&nbsp;  &nbsp;  &nbsp;  &nbsp;虽然说是将低维数据映射到高维，但是依旧是在低维度进行计算，免去了高维度计算的烦恼。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;本次记录为初学支持向量机的笔记，如有错误，望轻喷！！！**



