## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. AlexNet网络结构：
<br>

```cpp
Input 
Layer1 : conv + pool
Layer2 : conv + pool
Layer3 : conv
Layer4 : conv
Layer5 : conv + pool
Layer6 : Full
Layer7 : Full
Softmax Output
```
<br>

## 二. AlexNet网络每一层的参数：
<br>

### (一)Input： 

&nbsp;  &nbsp;  &nbsp;  &nbsp;227 x 227 x 3，输入图像的尺寸为227 x 227，3通道。
<br>

### （二）Layer1 :
&nbsp;  &nbsp;  &nbsp;  &nbsp;**CONV1** : 96个滤波器， 每个滤波器的尺寸为 11 x 11， 步长为4， pad 为0

**&nbsp;  &nbsp;  &nbsp;  &nbsp;从这些参数可以看出，第一层的卷积只是为了粗略地提取图像中的特征，非常大的滤波器尺寸，步长，得到的图像特征比较少。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;**MAX POOL1**: 使用3 x 3的滤波器，步长为2，进行特征压缩

&nbsp;  &nbsp;  &nbsp;  &nbsp;**NORM1**:归一化层
<br>

### （三）Layer2:
&nbsp;  &nbsp;  &nbsp;  &nbsp;**CONV2:** 256个滤波器，滤波器的尺寸为 5 x 5，步长为 1， pad 为2。从这些参数可以看出，这是为了提取更加多的图像特征。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;为什么滤波器的个数增加为256个呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp;因为第一层池化后，特征图的尺寸减小，即特征被压缩了。因此需要增大滤波器的个数，使特征图中的特征数保持平衡。有利于更好地表达特征。**

&nbsp;  &nbsp;  &nbsp;  &nbsp;**MAX POOL2**: 使用3 x 3的滤波器，步长为2，进行特征压缩。与之前的POOL选择同样的大小。

&nbsp;  &nbsp;  &nbsp;  &nbsp;**NORM2**:归一化层
<br>

### （四）Layer3:
&nbsp;  &nbsp;  &nbsp;  &nbsp;**CONV3**: 384个滤波器，滤波器的尺寸减小为3 x 3，步长为1， pad 为1。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;增大滤波器的个数，保持特征图中特征个数的平衡。滤波器的尺寸降低，步长降低，提取更多的特征。**
<br>

### （五）Layer4：
&nbsp;  &nbsp;  &nbsp;  &nbsp;**CONV4**: 384个滤波器，滤波器的尺寸减小为3 x 3，步长为1， pad 为1。与上一层的卷积层参数尺寸相同。
<br>

### （六）Layer5:
&nbsp;  &nbsp;  &nbsp;  &nbsp;**CONV5**: 256个滤波器，滤波器的尺寸减小为3 x 3，步长为1， pad 为1。

&nbsp;  &nbsp;  &nbsp;  &nbsp;**MAX POOL3:** 同样是3 x 3的滤波器，步长为2，与之前的POOL层参数相同。
<br>


### （七）Layer6:
&nbsp;  &nbsp;  &nbsp;  &nbsp;**FC6**: 4096个神经元。全连接层就是之前的经典神经网络。
<br>


### （八）Layer7:
&nbsp;  &nbsp;  &nbsp;  &nbsp;**FC7**: 4096个神经元。
<br>

### （九）Layer8:
&nbsp;  &nbsp;  &nbsp;  &nbsp;**FC8**: Softmax分类器，全连接输出1000 个神经元（类别数）

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072321221099.png)


 




