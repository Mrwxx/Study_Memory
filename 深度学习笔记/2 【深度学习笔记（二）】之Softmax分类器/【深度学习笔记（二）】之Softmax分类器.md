## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.	Softmax分类器
&nbsp;  &nbsp;  &nbsp;  &nbsp;用SVM损失函数得出的只是一个个的分数，还要通过对比分数来分类。那么，如果直接输出结果为分类的概率，岂不是更好？
&nbsp;  &nbsp;  &nbsp;  &nbsp;这里，给出了softmax分类器，直接输出分类的概率。

## 二．Sigmoid函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于概率是在【0，1】之间，这时就需要引入sigmoid函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;Sigmoid函数
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200716104144621.png)
&nbsp;  &nbsp;  &nbsp;  &nbsp;输入从负无穷到正无穷，输出在【0，1】之间。可以将得分值映射到【0，1】之间。
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200716104152547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
## 三．Softmax损失函数
### （一）公式
&nbsp;  &nbsp;  &nbsp;  &nbsp;Softmax分类器也是用损失函数来评估，并对分类的正确与否进行调整。
&nbsp;  &nbsp;  &nbsp;  &nbsp;损失函数：交叉熵损失
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200716104158808.png)
### （二）理解损失函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;如何理解这个损失函数呢？现在来举个例子：
&nbsp;  &nbsp;  &nbsp;  &nbsp;下面是一个对猫进行分类的结果，第一列数字是得分函数得出的分数，第二列是e^x^后的结果，第三列是归一化概率的结果。
#### 1. 对分数求e^x^
**&nbsp;  &nbsp;  &nbsp;  &nbsp;我们要根据这个分数先分别求e^x^， 因为得分函数得出的分数相差并不大，我们需要将得分大的数用e的次幂拉大与其他数的差距，即分数越大，可能性越大，结果越明显**。而越小的数，通过e的次幂，扩大的效果肯定不如大的分数；甚至小于0的分数，通过e的次幂缩放到可以忽略的地步。如第二列数字所示。
#### 2. 归一化概率
&nbsp;  &nbsp;  &nbsp;  &nbsp;接下来就是对e的次幂后的分数进行归一化操作，如何归一化呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp;就是用e的次幂后的三个分数，求每个分数的占三个分数的比例，即第三列数字。

#### 3. 损失函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于这次分类是错误的，猫识别为车的分数5.1要比正确分类3.2高，因此，需要损失函数来调整。注意：这里的损失函数是对正确分类cat的归一化概率0.13进行计算，**通过函数-log**，**若正确分类的概率值越接近于1，则说明分类效果很好，对应的损失函数-log也是接近于0的；若正确分类的概率越接近于0，则说明分类效果很差，对应的损失函数-log就越大**。

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200716104206452.png)
## 四. 总结
&nbsp;  &nbsp;  &nbsp;  &nbsp;Softmax分类器能够扩大分数的差距，即使得分函数的分数结果差别都不大，通过softmax分类器，就能够使得分数的差距进一步拉大，使得分类效果更加明显。



