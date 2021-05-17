## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	物体检测
### （一）定义
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;在输入图像中检测出所有的不同物体，并用方框将所有的物体都框出来。
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095737120.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

### （二）候选框（Region proposals）
&nbsp;  &nbsp;  &nbsp;  &nbsp;如果用分类和回归定位，那么要用滑动窗口滑动整张图片，需要计算太多的位置和预处理太多的图片尺寸，耗时耗力。

&nbsp;  &nbsp;  &nbsp;  &nbsp;**物体检测只需要将图片中的物体检测出来就可以了，图片中的物体个数不会太多，有些背景是不需要检测出来的，因此，我们只需要少量的窗口即可，将所需要的的物体给框出来，再从这些窗口中进行检测。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;这些窗口叫做“**候选框**“，从输入图像中将可能是物体的区域用不同尺寸的候选框框住，以方便后续的检测。

**下图中左图为输入图像，右图为所有的候选框**
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095746380.png)
<br>


### （三）生成候选框（Selective Search算法）
&nbsp;  &nbsp;  &nbsp;  &nbsp;**选择搜索算法用于生成候选框**，这个用的是图像处理中的图像分割算法，现将图像分割为很小的区域，**再将图像中相似的区域连接起来，生成候选框**。

如下图所示：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095754783.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

<br>


## 二．R-CNN模型
### （一）提取候选框
&nbsp;  &nbsp;  &nbsp;  &nbsp;提取出了大概2000个 Regions of Interest(RoI)
<br>

### （二）调整候选框的尺寸
&nbsp;  &nbsp;  &nbsp;  &nbsp;从图片中提取出的候选框的尺寸一定是不相同的，我们需要**调整这些候选框的尺寸，使得所有的候选框的尺寸相同。**
<br>


### （三）卷积神经网络
&nbsp;  &nbsp;  &nbsp;  &nbsp;经过卷积神经网络提取特征值，并将特征值固化在磁盘中。特征值大概200GB，非常麻烦。
<br>


### （四）分类
&nbsp;  &nbsp;  &nbsp;  &nbsp;用SVMs对磁盘中的特征值进行分类，**SVM是二分类的分类器**，那么有多少个类型，就需要多少个SVM分类器，非常麻烦。
<br>


### （五）回归
&nbsp;  &nbsp;  &nbsp;  &nbsp;也是用磁盘中的特征值进行回归定位，线性回归确定最佳位置。
<br>


### （六）R-CNN的问题
1. 测试时间非常慢

 **&nbsp;  &nbsp;  &nbsp;  &nbsp; 因为每个候选框都要进行一次CNN卷积神经网络操作，这样非常耗时间。**

2. 固化特征很麻烦

&nbsp;  &nbsp;  &nbsp;  &nbsp;将特征固化在磁盘中，再从磁盘中进行分类和回归，而不是直接完成的。

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095806713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

<br>



## 三．	Fast R-CNN

### （一）	共享CNN
&nbsp;  &nbsp;  &nbsp;  &nbsp;在R-CNN中，每个候选框独立进行CNN操作，非常耗时。Fast R-CNN进行了改进，首先进行CNN操作，再进行提取候选框操作，**这样，所有的候选框就是共享同一个CNN操作，提速非常多。**

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095815378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

<br>


### （二）	整合分类和回归
&nbsp;  &nbsp;  &nbsp;  &nbsp;R-CNN中是分别进行分类和回归的，**在Fast R-CNN中，我们将分类和回归整合在一起。用全连接层分出两个分支，一个用作分类，一个用作回归，同时分类损失和回归损失结合在一起，反向传播调整权重。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;这样，整个模型就是流水线的操作，减少了很多中间操作以及人为操作，**大大提高了速度以及模型的鲁棒性。**
 <br>


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095823750.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
<br>


### （三）R-CNN和 Fast R-CNN对比
&nbsp;  &nbsp;  &nbsp;  &nbsp;0.32是没有选取候选框的测试时间，2秒是加上选取候选框的测试时间。可以看到，选取候选框十分耗时。
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095830980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
<br>



## 三．	Faster R-CNN
&nbsp;  &nbsp;  &nbsp;  &nbsp;**Fast R-CNN最大的测试时间占比是选取候选框，那么将选取候选框集成到卷积神经网络中不就更好了么？**
<br>


### （一）改进
&nbsp;  &nbsp;  &nbsp;  &nbsp;**Faster R-CNN将选取候选框设置在CNN的最后一层卷积层之后，称为Region Proposal Network(RPN)，用卷积后得出的特征图来选取候选框**，然后做分类和回归操作。那么，所有的操作都在CNN中，没有其他分支，速度更加快。
<br>


### （二）RPN
**&nbsp;  &nbsp;  &nbsp;  &nbsp;在最后一层卷积得出的特征图中选出候选框。**

怎么做的呢？

&nbsp;  &nbsp;  &nbsp;  &nbsp;**同样是通过滑动窗口，只不过是在特征图上进行滑动，对于每个滑动窗口获取9个候选框，每个候选框的尺寸都是不一样的，它设置了9种候选框的尺寸大小，这些候选框的尺寸能够将正常生活中的物体都框出来。**

anchors就是候选框,n = 9

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072909584041.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)


### （三）	ROI POOLING
&nbsp;  &nbsp;  &nbsp;  &nbsp;提取完候选框后，我们就可以进行分类和回归了。但是，提取出的候选框尺寸都是不一样的，那么所得出的特征图的特征向量维度肯定是不同的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;**而分类和回归需要相同维度的特征向量，因此需要ROI POOLING对于不同尺寸的候选框进行不同的POOLING操作，压缩特征到同一维度。**
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095848908.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

### （四）	Faster R-CNN提升对比
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看出，Faster R-CNN的速度相对于R-CNN,Fast R-CNN都大幅度地提升，**甚至达到了实时的水平，而准确率并没有下降，反而有微小的提升。**

 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729095854842.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

