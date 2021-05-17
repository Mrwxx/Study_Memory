## 一．	Tensorflow2相关函数介绍
### （一）	tensorflow2中的自动求导机制
&nbsp;  &nbsp;  &nbsp;  &nbsp;tf.GradientTape()自动进行求导，在tf.GradientTape()的上下文内，所有的变量和计算步骤都会被记录用于求导。
<br>


```python
import tensorflow as tf
x = tf.Variable(initial_value=3.)
with tf.GradientTape() as tape: 
	y = tf.square(x)
	y_grad = tape.gradient(y, x) # 计算y关于x的导数
print([y, y_grad])
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;求导用tape.gradient(y, x)进行，求张量y 对变量x的导数。

输出是：

```python
[array([9.], dtype=float32), array([6.], dtype=float32)]
```
<br>



### （二）	设置优化器
&nbsp;  &nbsp;  &nbsp;  &nbsp;线性回归用SGD随机梯度下降进行优化：

```python
optimizer = tf.keras.optimizers.SGD(learning_rate=1e-3)
```
<br>



### （三）	自动根据梯度更新参数
&nbsp;  &nbsp;  &nbsp;  &nbsp;当求解出损失函数关于变量的梯度时，我们可以根据这些梯度进行更新参数，依靠的是优化器。注意：括号内的参数是损失函数对变量求导的梯度和变量的列表。

```python
optimizer.apply_gradients(grads_and_vars=zip(grads, variables))
```
<br>



### （四）	zip函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;zip（a, b）将a和b对应的数都合成一个个元组，返回由这些元组组成的列表。
如：

```python
a = [1, 3, 5]  ，  b = [2, 4, 6]  ，那么  zip(a, b) = [(1, 2), (3, 4), …, (5, 6)]
```
<br>



### （五）	变量
&nbsp;  &nbsp;  &nbsp;  &nbsp;线性回归中要不断地更新参数，参数需要用变量来设置。

```python
a = tf.Variable(initial_value=0.)
5. b = tf.Variable(initial_value=0.)
```
<br>



## 二．	tf2实现线性回归
<br>



```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt

#随机1000个点，y = 0.1 x + 0.3
num_points = 1000
vector_set = []
for i in range(num_points):
    x1 = np.random.normal(0.0, 0.55)
    y1 = x1 * 0.1 + 0.3 + np.random.normal(0., 0.03)
    vector_set.append([x1, y1])

#生成样本
x_data = [v[0] for v in vector_set]
y_data = [v[1] for v in vector_set]

#画图显示
plt.scatter(x_data, y_data, c='r')
plt.show()

#参数
w = tf.Variable(tf.random.uniform([1], -1.0, 1.0), name='w')
b = tf.Variable(tf.zeros([1], name='b'))
#需要更新的变量
variables = [w, b]

#采用梯度下降法优化参数
#优化器
opt = tf.keras.optimizers.SGD(1e-3)
#迭代次数
num_epoch = 10000
for e in range(num_epoch):
    #使用tf.GradientTape()记录损失函数的梯度信息
    with tf.GradientTape() as tape:
        #预测值
        y = w * x_data + b
        #损失值
        loss = tf.reduce_mean(tf.square(y - y_data), name='loss')
    #自动计算loss 关于更新变量的梯度
    grads = tape.gradient(loss, variables)
    #自动根据梯度更新参数
    opt.apply_gradients(zip(grads, variables))
    if(e % 100 == 0):
        print(variables)
```

<br>


## 三．	结果校验
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;我们设置的1000个随机点分布在直线y  =0.1x + 0.3 周围。得出的结果不断迭代，如下所示：

```python
[<tf.Variable 'w:0' shape=(1,) dtype=float32, numpy=array([0.10239088], dtype=float32)>, <tf.Variable 'Variable:0' shape=(1,) dtype=float32, numpy=array([0.29980734], dtype=float32)>]
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，10000次迭代后，w参数变为了0.10239088， b参数变为了0.29980734，已经非常地接近正确的参数值了。









