## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	模型的构建
### （一）	keras中的Model
&nbsp;  &nbsp;  &nbsp;  &nbsp;Model用于构建模型，即将各种层进行组织和连接，并封装成一个整体，即模型，将输入数据通过各种层得到输出。

&nbsp;  &nbsp;  &nbsp;  &nbsp;我们可以将模型通过继承Model，这个类类定义自己的模型，在继承类中，我们需要重写构造函数__init__()和模型调用函数call(input)，还可以添加自定义的方法。
<br>



```python
class MyModel(tf.keras.Model):
	def __init__(self):
		super().__init__() 
 		# 此处添加初始化代码（包含 call 方法中会用到的层），例如
		 # layer1 = tf.keras.layers.BuiltInLayer(...)
		 # layer2 = MyCustomLayer(...)
	def call(self, input):
 		# 此处添加模型调用的代码（处理输入并返回输出），例如
 		# x = layer1(input)
 		# output = layer2(x)
	return output
 	# 还可以添加自定义的方法
```
<br>



### （二）	keras中的layers
&nbsp;  &nbsp;  &nbsp;  &nbsp;layers将各种计算流程和变量进行了封装，内置了大量常用的预定义层，如卷积层，池化层，全连接层等等。
<br>




## 二．	构建模型类
&nbsp;  &nbsp;  &nbsp;  &nbsp;将线性回归的模型封装到一个类中。
<br>



### （一）代码
<br>



```python
import tensorflow as tf

x = tf.constant([[1., 2., 3.], [4., 5., 6.]])
y = tf.constant([[10.], [20.]])

class Linear(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.dense = tf.keras.layers.Dense(
            units =1,       #输出维度
            activation=None,    #激活函数
            kernel_initializer=tf.zeros_initializer(),  #权重参数
            bias_initializer=tf.zeros_initializer       #偏置参数
        )

    def call(self, input):
        output = self.dense(input)
        return output

model = Linear()
opt = tf.keras.optimizers.SGD(1e-3)
for i in range(10000):
    with tf.GradientTape() as tape:
        #调用封装的模型类实例
        y_pred = model(x)   
        loss = tf.reduce_mean(tf.square(y_pred - y))
    grads = tape.gradient(loss, model.variables)    #调用model.variables直接得到模型中的所有变量
    opt.apply_gradients(zip(grads, model.variables))
print(model.variables)
```
<br>



### （二）代码分析
#### 1. 封装模型类的__init()__函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;**__init()__函数添加的是模型的层的定义，将需要用到的层定义到该函数中。**

**定义一个全连接层，全连接层的参数为：**

&nbsp;  &nbsp;  &nbsp;  &nbsp;**units**:输出张量的维数

&nbsp;  &nbsp;  &nbsp;  &nbsp;**activation**:激活函数，一般有tf.nn.relu, tf.nn.tanh, tf.nn.sigmoid

&nbsp;  &nbsp;  &nbsp;  &nbsp;**kernel_initializer, bias_initialize**r：权重矩阵kernel和偏置向量bias两个变量的初始化器。
<br>



#### 2. 模型类的call(input)函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;**使用__init__函数中定义的层，对输入数据进行计算，得到输出。**
<br>



#### 3. model.variables
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;Model是我们的模型类的实例，model.variables可以得出该模型类中的所有变量。




