## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## **一. 问题**:
&nbsp;  &nbsp;  &nbsp;  &nbsp;如何记忆TP，FP，FN，TN 四种判断类型，如何不混淆这4种类型？
## 二. 记忆方法
### 1. TP
&nbsp;  &nbsp;  &nbsp;  &nbsp;True Positive，从英文名可以看出，首先是**true**，正确的，说明判断正确；再看后面的是**Positive**，正类，那么联系前文可知是判断正确的，即**将正类判断为正类**。
### 2. FP
&nbsp;  &nbsp;  &nbsp;  &nbsp;False Positive，同理，首先是**False**,错误的，说明判断错误；再看后者，**Positive**，正类，那么联系前文可以记忆将**负类判断错误为正类**。
### 3. FN
&nbsp;  &nbsp;  &nbsp;  &nbsp;False Negative。首先是**False**，判断错误；再看后者，**Negative**,负类，可以记忆为将**正类判断错误为负类**。
### 4. TN
&nbsp;  &nbsp;  &nbsp;  &nbsp;True Negative,首先是**True**,判断正确；再看后者，**Negative**,负类，可以记忆为**负类判断为负类**。
<br>
## 三.  根据四种判断类型计算的指标

### 1. 准确率    
&nbsp;  &nbsp;  &nbsp;  &nbsp;Accuracy=（TP+TN） / （TP+FP+TN+FN）， 即预测正确的比上所有的数据

### 2. 精确率    
&nbsp;  &nbsp;  &nbsp;  &nbsp;Precision=TP / （TP+FP），即在预测为正例的数据中，有多少是预测正确。

### 3. 召回率  
  &nbsp;  &nbsp;  &nbsp;  &nbsp;Recall=TP / （TP+FN），即在所有正例的数据中，有多少是预测正确的。
*
