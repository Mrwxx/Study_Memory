## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	VGG模型
 &nbsp;  &nbsp;  &nbsp;  &nbsp;前面已经介绍过了VGG网络模型，一共13层，这里说的13层指的是10层卷积层和3层全连接层，并没有包括池化层。下面代码详细地将VGG的13层网络模型复现，并用CIFAR100数据集进行训练，测试。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;代码中附有详细的注释，从数据的预处理，到训练，再到测试。
<br>

## 二．	代码实现
<br>

```python
import tensorflow as tf
from tensorflow.keras import layers, optimizers, datasets, Sequential
import os

os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'
tf.random.set_seed(2345)

#VGG13总共13层，指的是10层卷积层和3层全连接层

"""
一共5个单元的卷积层和池化层，每个单元2个卷积层和一个池化层
"""

#卷积层和池化层
conv_layers = [

    #第一个单元 2个卷积和一个池化

    layers.Conv2D(64, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.Conv2D(64, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.MaxPool2D(pool_size=[2, 2], strides=2, padding="same"),

    #第二个单元
    layers.Conv2D(128, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.Conv2D(128, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.MaxPool2D(pool_size=[2, 2], strides=2, padding="same"),

    #第三个单元
    layers.Conv2D(256, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.Conv2D(256, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.MaxPool2D(pool_size=[2, 2], strides=2, padding="same"),

    #第四个单元
    layers.Conv2D(512, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.Conv2D(512, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.MaxPool2D(pool_size=[2, 2], strides=2, padding="same"),

    #第五个单元
    layers.Conv2D(512, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.Conv2D(512, kernel_size=[3, 3], padding="same", activation=tf.nn.relu),
    layers.MaxPool2D(pool_size=[2, 2], strides=2, padding="same"),

]

#数据预处理
def preprocess(x, y):
    #数据到0-1之间
    x = tf.cast(x, dtype=tf.float32) / 255.
    y = tf.cast(y, dtype=tf.int32)
    return x,y

#数据自动下载加载
(x, y), (x_test, y_test) = datasets.cifar100.load_data()
print(x.shape, y.shape, x_test.shape, y_test.shape)
#标签 (64, 1)，需要squeeze掉 1
y = tf.squeeze(y, axis=1)
y_test = tf.squeeze(y_test, axis=1)

#数据的处理
#训练数据的处理
train_db = tf.data.Dataset.from_tensor_slices((x, y))
train_db = train_db.shuffle(1000).map(preprocess).batch(64)
#测试数据的处理
test_db = tf.data.Dataset.from_tensor_slices((x_test, y_test))
test_db = test_db.map(preprocess).batch(64)

#取出一个sample查看 每个样本数据的尺寸，标签的尺寸，最大值最小值
sample = next(iter(train_db))
print("sample:", sample[0].shape, sample[1].shape,
      tf.reduce_min(sample[0]), tf.reduce_max(sample[0]))




def main():

    #卷积层
    conv_net = Sequential(conv_layers)

    #test卷积层和池化层
    #conv_net.build(input_shape=[None, 32, 32, 3])
    # x = tf.random.normal([4, 32, 32, 3])
    # out = conv_net(x)
    # print(out.shape)

    #全连接层
    fc_net = Sequential([
        layers.Dense(256, activation=tf.nn.relu),
        layers.Dense(128, activation=tf.nn.relu),
        layers.Dense(100, activation=None),
    ])

    conv_net.build(input_shape=[None, 32, 32, 3])
    fc_net.build(input_shape=[None, 512])

    #设置优化器，优化学习率
    optimizer = optimizers.Adam(lr=1e-4)

    #每个层键的参数变量结合起来
    variables = conv_net.trainable_variables + fc_net.trainable_variables

    #处理完数据后，开始训练
    for epoch in range(50):
        for step, (x, y) in enumerate(train_db):
            #自动求解梯度
            with tf.GradientTape() as tape:
                #卷积层和池化层
                #[b, 32, 32, 3] -> [b, 1, 1, 512]
                out = conv_net(x)
                #flatten -> [b, 512]
                out = tf.reshape(out, [-1, 512])
                #全连接层
                #[b, 512] -> [b, 100]
                logits = fc_net(out)

                #走完卷积层，池化层，全连接层得到输出值
                #求解Loss值,需要将y的尺寸补齐到logits的尺寸
                #[b] -> [b, 100]
                y_onehot = tf.one_hot(y, depth=100)
                loss = tf.losses.categorical_crossentropy(y_onehot, logits, from_logits=True)
                #计算Loss的均值，每个样本有一个loss均值
                loss = tf.reduce_mean(loss)

            #反向传播，对所有的层间参数进行求导
            grads = tape.gradient(loss, variables)
            #更新梯度
            optimizer.apply_gradients(zip(grads, variables))

            #每100个样本打印一次结果
            if step%100 == 0:
                print(epoch, step, "loss:", float(loss))

        #测试模型
        total_num = 0
        total_correct = 0
        for x, y in test_db:
            out = conv_net(x)
            out = tf.reshape(out, [-1, 512])
            logits = fc_net(out)
            #softmax转换成概率
            prob = tf.nn.softmax(logits, axis=1)
            #选择概率最大的作为预测值
            pred = tf.argmax(prob, axis=1)
            pred = tf.cast(pred, dtype=tf.int32)

            #计算正确预测的数量
            correct = tf.cast(tf.equal(pred, y), dtype=tf.int32)
            correct = tf.reduce_sum(correct)

            total_num += x.shape[0]
            total_correct += int(correct)

        #计算正确率
        acc = total_correct / total_num
        print(epoch, "acc:", acc)



if __name__ == '__main__':
    main()
```



