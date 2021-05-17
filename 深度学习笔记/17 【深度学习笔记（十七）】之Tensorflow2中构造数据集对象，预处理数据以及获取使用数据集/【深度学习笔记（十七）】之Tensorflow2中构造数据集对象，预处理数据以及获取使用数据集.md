## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	创建数据集对象
### （一）tf.data.Dataset的组成
&nbsp;  &nbsp;  &nbsp;  &nbsp;tensorflow2中通过tf.data.Dataset类对数据集进行了封装。tf.data.Dataset由一系列的可迭代访问的元素组成，每个元素包含一个或多个张量。如图像数据集中，每个元素可以是形状为 长 x 宽 x 通道数 的图片张量，也可以是由图片张量和图片标签张量组成的元组。
<br>


### （二）创建tf.data.Dataset对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;当数据量较小时，可以通过tf.data.Dataset.from_tensor_slices()创建tf.data.Dataset对象，传入一个张量或者多个第0维大小相同的张量，即可按张量的第0维展开构建数据集，数据集的元素数量为张量第0维的大小。当提供多个张量作为输入时，张量的第0维大小必须相同，且必须将多个张量作为元组拼接并作为输入。

如：

```python
import tensorflow as tf
import numpy as np
.
X = tf.constant([2013, 2014, 2015, 2016, 2017])
Y = tf.constant([12000, 14000, 15000, 16500, 17500])

dataset = tf.data.Dataset.from_tensor_slices((X, Y))

for x, y in dataset:
	print(x.numpy(), y.numpy())
```
<br>



### （三）展示mnist_dataset数据集的图片和标签
<br>



```python
import tensorflow as tf
import matplotlib.pyplot as plt
import numpy as np
(train_data, train_label), ( test_data, test_label ) = tf.keras.datasets.mnist.load_data()
train_data = np.expand_dims(train_data.astype(np.float32) / 255., axis=-1)
mnist_dataset = tf.data.Dataset.from_tensor_slices((train_data, train_label))

#mnist_dataset = mnist_dataset.map()
for image, label in mnist_dataset:
    plt.title(label.numpy())
    plt.imshow(image.numpy()[:, :, 0])
    plt.show()
```
<br>




## 二．	数据集对象的预处理
### （一）	Dataset.map(fun)
&nbsp;  &nbsp;  &nbsp;  &nbsp;对数据集对象中的每个元素应用函数fun，得到一个新的数据集。

```python
def rot90(image, label):
	image = tf.image.rot90(image)
	return image, label

mnist_dataset = mnist_dataset.map(rot90)

for image, label in mnist_dataset:
	plt.title(label.numpy())
	plt.imshow(image.numpy()[:, :, 0])
	plt.show()
```
<br>



### （二）	Dataset.shuffle(buffer_size)
&nbsp;  &nbsp;  &nbsp;  &nbsp;打乱数据集，设定一个buffer_size大小的缓冲区，取出前buffer_size个元素放到缓冲区中，再从缓冲区中随机采样，后续的数据填补空缺。当数据集的顺序分布较为随机，则缓冲区的大小可较小，否则需要大的缓冲区。
<br>



```python
mnist_dataset = mnist_dataset.shuffle(buffer_size=10000).batch(4)

for images, labels in mnist_dataset:
	fig, axs = plt.subplots(1, 4)
	for i in range(4):
		axs[i].set_title(labels.numpy()[i])
		axs[i].imshow(images.numpy()[i, :, :, 0])
	plt.show()
```
<br>



### （三）	Dataset.batch(batch_size)
&nbsp;  &nbsp;  &nbsp;  &nbsp;将数据集的数据按batch_size分成多个批次，每batch_size个元素，使用tf.stack()在第0位合并为一个元素。
<br>



```python
mnist_dataset = mnist_dataset.batch(4)

for images, labels in mnist_dataset: # image: [4, 28, 28, 1], labels: [4]
fig, axs = plt.subplots(1, 4)
for i in range(4):
	axs[i].set_title(labels.numpy()[i])
	axs[i].imshow(images.numpy()[i, :, :, 0])
plt.show()
```

<br>



## 三．	获取数据集的元素
### （一）	for循环
&nbsp;  &nbsp;  &nbsp;  &nbsp;tf.data.Dataset是一个可迭代的对象，可以使用for循环迭代获取数据。
<br>



### （二）	iter()
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用iter()显示创建一个迭代器，并使用next()获取下一个元素：
<br>



```python
dataset = tf.data.Dataset.from_tensor_slices((A, B, C, ...))
it = iter(dataset)
a_0, b_0, c_0, ... = next(it)
a_1, b_1, c_1, ... = next(it)
```
<br>



## 四．	使用数据集对象
&nbsp;  &nbsp;  &nbsp;  &nbsp;keras支持使用tf.data.Dataset作为输入，如fit()函数需要传入训练数据，标签数据，迭代次数以及每批数据的数量。
<br>



```python
model.fit(x=train_data, y=train_label, epochs=num_epochs,
batch_size=batch_size)
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;我们可以直接传入Dataset，在此之前通过Dataset.batch(batch_size)进行分批，因此，fit（）函数中只需要传入Dataset以及迭代次数即可：

```python
model.fit(mnist_dataset, epochs=num_epochs)
```


