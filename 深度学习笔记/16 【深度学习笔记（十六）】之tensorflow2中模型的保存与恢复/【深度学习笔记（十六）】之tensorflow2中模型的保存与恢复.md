## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	tensorflow2中模型的保存
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Tensorflow2中提供了 tf.train,Checkpoint这个变量保存与恢复类，可以使用save()和restore()方法将 Tensorflow中所有包含CheckPointable State的对象进行保存和恢复。

&nbsp;  &nbsp;  &nbsp;  &nbsp;如，优化器tf.keras.optimizer，变量tf.Variable，层tf.keras.Layer和模型tf.keras.Model实例都可以被保存。
<br>


### （二）Checkpoint对象的保存
&nbsp;  &nbsp;  &nbsp;  &nbsp;要保存CheckPoint对象，首先需要声明一个Checkpoint，tf.train.Checkpoint()接受的初始化参数是一系列的键值对，键名任意取，值为需要保存的对象。这里的键名非常重要，在恢复模型时，需要使用同样的键名。
	

```python
checkpoint = tf.train.Checkpoint(model=model)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;在模型训练完成后，对模型进行保存，使用save函数保存，参数为保存的目录名。

```python
checkpoint.save(path)
```

如：

```python
checkpoint.save(‘./save/model.ckpt’)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;save目录下就会生成三个文件：

```python
checkpoint
model.ckpt-1.index
model.ckpt-1.data-00000-of-00001
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;save()方法能够运行多次，每次运行后都能得到一个.index文件和一个.data文件，文件名的序号依次增加。

<br>



## 二．	Checkpoint对象的恢复
### （一）恢复模型
&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们需要为某个模型加载之前已经训练好的参数时，需要再次实例化一个checkpoint，键名需要和保存的键名一致，再调用restore()方法恢复模型参数，restore()方法的参数为之前保存的文件的目录+编号。<br>


&nbsp;  &nbsp;  &nbsp;  &nbsp;如”./save/model.ckpt-1”，跟之前保存的目录名相比添加了编号1，即第一个保存的文件。

如：

```python
#待恢复参数的同一模型
model_to_be_restored = MyModel()
#新建checkpoint
checkpoint = tf.train.Checkpoint(model=model_to_be_restored)
checkpoint.restore(path_index)
```
<br>


### （二）恢复最近的一个Checkpoint
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用tf.train.latest_checkpoint(path)返回目录下最近一次checkpoint的文件名。

如：

```python
checkpoint.restore(tf.train.latest_checkpoint(“./save”))
```

<br>




## 三．保存与恢复变量代码模板

&nbsp;  &nbsp;  &nbsp;  &nbsp;先实例化checkpoint，继而训练，保存模型，再在其他模型中恢复保存的参数。

train.py

```python
model = MyModel()
checkpoint = tf.train.CheckPoint((myModel=model)
#训练模型
checkpoint.save(“./save/model.ckpt”)
```

test.py

```python
model = MyModel()
checkpoint = tf.train.CheckPoint(myModel=model)
checkpoint.restore(tf.train.latest_checkpoint(“./save”))
#模型使用
```
<br>




## 四．	管理checkpoint
&nbsp;  &nbsp;  &nbsp;  &nbsp;当checkpoint保存的数量过多时，我们可能只想保存最后几个checkpoint，或者希望使用其他的编号方式，这些都要通过checkpointManager来实现。
<br>



```python
checkpoint = tf.train.Checkpoint(model=model)
manager = tf.train.CheckpointManager(checkpoint, directory=”./save”, checkpoint_name=’model.ckpt’, max_to_keep=k)
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;**directory**  ：文件保存的路径
&nbsp;  &nbsp;  &nbsp;  &nbsp;**checkpoint_name** ：文件名前缀（不提供则默认为
ckpt  ）
&nbsp;  &nbsp;  &nbsp;  &nbsp;**max_to_keep** ：保留的Checkpoint数目。
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;然后使用manager,save()保存即可，如需要修改编号的形式，则在保存时添加checkpoint_number参数，如：将编号设置为100

```python
manager.save(checkpoint_number=100)
```




