## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">



## 一．	Tensorflow2构建网络以及训练，测试流程
<br>


1.	使用 tf,keras,datasets 获取数据集并预处理

2.	使用 tf.keras,Model 和 tf.keras.layers 搭建模型

3.	训练模型，使用tf.keras.losses计算损失函数，使用tf.keras.optimizer优化模型

5.	测试模型，使用tf.keras.metrics 计算评估指标

<br>



## 二．	获取数据集以及预处理数据
### （一）	获取数据集
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以直接在代码中下载，也可以提前下载好数据集mnist.npz文件，放入用户目录下的 .keras/dataset目录下。（Windows目录为 C:\\Users\\用户名），Linux目录为 /home/用户名

```python
mnist = tf.keras.datasets.mnist
```
<br>



### （二）	查看训练数据和测试数据
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取到数据集后，我们可以查看一下数据集的训练数据和测试数据，通过load_data()方法返回两个元组，每个元组包括训练集合测试集的数据和标签。

```python
(train_data, train_label), (test_data, test_label) = mnist.load_data()
```
<br>



### （三）	数据预处理
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;根据项目的实际情况进行预处理。此处的mnist数据集中读出的数据缺少一个颜色通道数维度，因此需要扩大一个维度，并且将灰度值归一化到0-1之间。
		

```python
train_data = np.expand_dims(train_data.astype(np.float32) / 255., axis=-1)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这里我们将数据集的获取与数据预处理封装到一个类中，方便代码的复用。
<br>



```python
#数据获取与预处理类
class MNISTLoader():
    def __init__(self):
        mnist = tf.keras.datasets.mnist     #获取已经下载好的数据集
        #获取训练数据，标签以及测试数据，标签
        (self.train_data, self.train_label), (self.test_data, self.test_label) = mnist.load_data()
        #数据是三维的，少一位通道数，我们需手动添加一维
        #图像数据为0-255，需要归一化到0-1之间
        self.train_data = np.expand_dims(self.train_data.astype(np.float32) / 255., axis=-1)
        self.test_data = np.expand_dims(self.test_data.astype(np.float32) / 255., axis=-1)
        self.train_label = self.train_label.astype(np.int32)
        self.test_label = self.test_label.astype(np.int32)
        #训练数据数量，测试数据数量
        self.num_train_data, self.num_test_data = self.train_data.shape[0], self.test_data.shape[0]

    def get_batch(self, batch_size):
        #随机取batch_size数据以及标签
        #随机取索引
        index = np.random.randint(0, self.num_train_data, batch_size)
        return self.train_data[index, :], self.train_label[index]
```

<br>



## 三．	搭建模型
&nbsp;  &nbsp;  &nbsp;  &nbsp;同样是将模型的搭建封装在一个类中，继承tf.keras.Model类。重写 __init__()函数，该函数中定义模型的各个层；重写call()函数，该函数定义了各个层间的操作。

&nbsp;  &nbsp;  &nbsp;  &nbsp;第一个层为Flatten层，将拉平数据的后三个维度称为一体

&nbsp;  &nbsp;  &nbsp;  &nbsp;第二个层为全连接层，神经元为100个，激活函数为relu函数

&nbsp;  &nbsp;  &nbsp;  &nbsp;第三个层为全连接层，神经元为10个，即分类树，没有激活函数

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，最后的全连接层输出的结果通过softmax函数求出概率。
<br>



```python
class MyModel(tf.keras.Model):
    def __init__(self):
        super().__init__()
        #Flatten层将除第一维外的维度展平
        self.flatten = tf.keras.layers.Flatten()
        self.dense1 = tf.keras.layers.Dense(units=100, activation=tf.nn.relu)
        self.dense2 = tf.keras.layers.Dense(units=10)

    def call(self, inputs):             #[batch_size, 28, 28, 1]
        x = self.flatten(inputs)        #[batch_size, 784]
        x = self.dense1(x)              #[batch_size, 100]
        x = self.dense2(x)              #[batch_size, 10]
        output = tf.nn.softmax(x)       #归一化求概率
        return output
```

<br>



## 四．	训练模型
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;选好batch，学习率，循环次数。将前面定义好的模型类，数据加载类实例化，同时定义一个模型的优化器tf.keras.optimizers.Adam。

训练步骤
1.	随机抽取一个batch的训练数据

2.	计算模型的预测值

3.	计算loss，计算loss对variable的导数

5.	将导数和参数传入优化器，更新模型的参数以最小化loss
<br>



```python
#循环次数
num_epoch = 5
#一个batch的数据量
batch_size = 50
#学习率
learning_rate = 1e-3

#模型实例
model = MyModel()
#数据加载器实例
data_loader = MNISTLoader()
#优化器
optimizer = tf.keras.optimizers.Adam(learning_rate)

#训练接中batch的数量
num_batch = int(data_loader.num_train_data / batch_size)

#训练过程
for i in range(num_epoch):
    for batch_index in range(num_batch):
        #导入一个batch的训练数据和标签
        x, y = data_loader.get_batch(batch_size)
        with tf.GradientTape() as tape:
            y_pred = model(x)       #预测值
            #计算损失值
            loss = tf.keras.losses.sparse_categorical_crossentropy(y_true=y, y_pred=y_pred)
            loss = tf.reduce_mean(loss)
            print("batch %d: loss %f" %(batch_index, loss.numpy()))
        #计算梯度
        grads = tape.gradient(loss, model.variables)
        optimizer.apply_gradients(zip(grads, model.variables))
```
<br>




## 五．	测试模型
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用tf.keras.metrics评估模型，使用对应的评估器在测试集上测试性能，将真实结果与预测结果比较，输出预测正确的样本占总样本数的比例。

**&nbsp;  &nbsp;  &nbsp;  &nbsp;model.predict()是预测输入值的标签，然后通过传入参数y_pred预测值（预测的标签）, y_true真实值（真实的标签），update_state()更新评估器的状态，迭代结束后，通过result()方法输出准确率。**

**&nbsp;  &nbsp;  &nbsp;  &nbsp;注意：update_state()传入的两个参数是标签，而不是数据，这是重点！！！**
<br>



```python
#模型的测试
#实例化对应损失函数的评估器
tester = tf.keras.metrics.SparseCategoricalAccuracy()

#测试集的batch数量
num_batch_test = int(data_loader.num_test_data / batch_size)
for batch_index in range(num_batch_test):
    #测试数据的标签
    start_index, end_index = batch_index * batch_size, (batch_index+1) * batch_size
    #预测值
    y_pred = model.predict(data_loader.test_data[start_index : end_index])
    #更新评估器的状态
    tester.update_state(y_true=data_loader.test_label[start_index:end_index], y_pred=y_pred)
    print("test accuracy : %f" % tester.result())
```


<br>

**结果：**

```python
test accuracy : 0.975900
```








