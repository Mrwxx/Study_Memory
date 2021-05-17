## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Keras Sequential
&nbsp;  &nbsp;  &nbsp;  &nbsp;简化tensorflow2的网络搭建与训练过程，用Sequential API将网络的各个层按照顺序叠加起来，只需要提供包含各个层的列表，Keras就可将它们自动首尾相连，并返回模型。
<br>


```python
model = tf.keras.models.Sequential([
	tf.keras.layers.Flatten(),
	tf.keras.layers.Dense(100, activation=tf.nn.relu),
	tf.keras.layers.Dense(10),
	tf.keras.layers.Softmax()
])
```
<br>




## 二．	Keras 的 Functional API
&nbsp;  &nbsp;  &nbsp;  &nbsp;为了构建更复杂的模型，使用Functional API，将层作为可调用的对象返回张量，并将输入向量和输出向量提供给 tf.keras.Model 的 inputs 和 outputs 参数。即按照网络的顺序调用各个层，最后将输入和输出放到tf.keras.Model参数中。

<br>



```python
inputs = tf.keras.Input(shape=(28, 28, 1))
x = tf.keras.layers.Flatten()(inputs)
x = tf.keras.layers.Dense(units=100, activation=tf.nn.relu)(x)
x = tf.keras.layers.Dense(units=10)(x)
outputs = tf.keras.layers.Softmax()(x)
model = tf.keras.Model(inputs=inputs, outputs=outputs)
```
<br>



## 三．	compile 配置训练过程
&nbsp;  &nbsp;  &nbsp;  &nbsp;按照上面将网络模型配置好以后，可以通过tf.keras.Model 的 compile方法配置训练过程，即设置训练优化器，计算损失函数以及评估指标。

tf.keras.Model.compile  接受 3 个重要的参数:

1.	optimizer : 优化器， 从 tf.keras.optimizers中选择

2.	loss : 损失函数， 从 tf.keras.losses中选择

4.	metrics: 评估指标，从 tf.keras,metrics中选择
<br>



```python
model.compile(
	optimizer=tf.keras.optimizers.Adam(learning_rate=0.001),

	loss=tf.keras.losses.sparse_categorical_crossentropy,
	metrics=[tf.keras.metrics.sparse_categorical_accuracy]
)
```
<br>



## 四．	fit方法开始训练
&nbsp;  &nbsp;  &nbsp;  &nbsp;tf.keras.Model.fit 方法开始训练模型，该方法接受5个参数：

1.	x： 训练数据

2.	y： 训练数据标签

3.	epochs: 迭代次数

4.	batch_size : 每一批的数据量

6.	validation_data:  验证数据，在训练过程中监控模型的性能
<br>



```python
model.fit(data_loader.train_data, data_loader.train_label,
epochs=num_epochs, batch_size=batch_size)
```
<br>




## 五．	evaluate 评估训练效果
&nbsp;  &nbsp;  &nbsp;  &nbsp;训练完后，用测试数据和测试标签对模型的性能进行测试，只需两个参数：

1.	测试数据

3.	测试数据标签
<br>



```python
model.evaluate(data_loader.test_data, data_loader.test_label)
```





