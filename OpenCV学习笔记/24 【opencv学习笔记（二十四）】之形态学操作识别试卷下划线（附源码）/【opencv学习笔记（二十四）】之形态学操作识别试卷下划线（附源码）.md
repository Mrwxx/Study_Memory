## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 需求：
&nbsp;  &nbsp;  &nbsp;  &nbsp;从试卷答题纸上匹配到填空题的下划线，并标记出所有直线。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200715230137595.png)

## 二. 思路：
&nbsp;  &nbsp;  &nbsp;  &nbsp;直接用边缘查找，然后霍夫变换直线的方法是行不通的，检测效果不好。
**&nbsp;  &nbsp;  &nbsp;  &nbsp;我们的解决思路是：通过形态学操作来寻找直线，然后用霍夫变换标定直线。**

## 三. 具体操作：
### （一）
&nbsp;  &nbsp;  &nbsp;  &nbsp;首先对图像进行二值化，能使直线与字显示地更加明显。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200715230218108.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

### （二）
&nbsp;  &nbsp;  &nbsp;  &nbsp;要想将直线很清晰地选出来，就要将其他数字和字母都去掉，这时，就可以用到**形态学的开操作**。**开操作为先腐蚀，再膨胀，能够消除细小物体**。因为直线的结构与其他字母和数字相比很特别，是一个很长的矩形，因此用一个长矩形结构就可以消除其他的字母和数字，并将直线选出来。
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020071523022746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果结果中还是有一些噪音干扰，可以调节结构体的长度，高度。

### （三）
&nbsp;  &nbsp;  &nbsp;  &nbsp;提取出了直线，还需要用膨胀操作对直线进行增强，使直线更加明显。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200715230235790.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，直线明显得到了增强，更加明显了。


### （四）
&nbsp;  &nbsp;  &nbsp;  &nbsp;对直线进行霍夫变换，标定出原图试卷中的直线。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200715230241837.png)
 
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，原图中的直线被清晰完整地标记了出来，我们已经达到了需求，之后可以根据这些直线的位置，框出直线上的矩形位置，即把学生们回答的答案用矩形框了出来。

&nbsp;  &nbsp;  &nbsp;  &nbsp;**源码获取，公号回复“opencv源码2”**




