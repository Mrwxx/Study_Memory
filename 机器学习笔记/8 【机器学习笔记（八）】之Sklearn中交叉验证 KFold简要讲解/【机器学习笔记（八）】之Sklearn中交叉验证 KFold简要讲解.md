## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## Sklearn中交叉验证API KFold讲解
### 一. 交叉验证原理讲解
#### 1. 设置验证集的原因

 &nbsp;  &nbsp;  &nbsp;  &nbsp;在机器学习建模过程中，将数据分为**训练集和测试集**。测试集合训练集是完全分开的两个数据集，完全不参与训练，只是用于模型最终确定后，来测试模型的效果。而训练集又要分出一部分数据用来验证模型的训练效果，即**验证集**。验证集在每次训练集训练结束后，对模型的效果进行初步地测试。之所以要设置验证集，是因为训练数据会有**过拟合**的情况出现，即训练数据能够很好地匹配训练数据，但对于训练数据之外的数据效果非常差。验证集不参与训练，可以**客观地评价模型对于训练集之外的数据的匹配度**。

#### 2. 交叉验证原理

 &nbsp;  &nbsp;  &nbsp;  &nbsp;交叉验证经常用于数据的验证，原理是**将数据分为 n 组**，每组数据都要作为一次验证集进行一次验证，而其余的 n-1 组数据作为训练集。这样一共要循环 n 次，**验证 n 次，得到 n 个模型**，这 n 个模型得到的 n 个误差**计算均值**，得到交叉验证误差。

### 二. API 讲解
##### 1. 导入

```python
from sklearn.model_selection import KFold
```
##### 2. 参数
###### （1）n_splits:

 &nbsp;  &nbsp;  &nbsp;  &nbsp;将训练集分为 n 份，n份数据，每一份都要作为作为一次验证集来验证训练的结果，一共 n 次循环，其余n-1份数据作为训练集进行训练。
###### (2).  shuffle:

 &nbsp;  &nbsp;  &nbsp;  &nbsp;表示是否打乱数据的顺序 ，bool 类型。
###### (3).  random_state: 

 &nbsp;  &nbsp;  &nbsp;  &nbsp;同一个数字保证每次循环都是分成同样的份。

##### 3. 调用：

```python
kf = KFold(n_splits=3, shuffle=False, random_state=None)
```


 &nbsp;  &nbsp;  &nbsp;  &nbsp;**返回 train_index, test_index，训练集的索引，验证集的索引**
##### 4. 使用：

```python
for train_index, test_index in kf.split(titanic):
	"""
```

