## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


### 一. PyTorch的安装命令
 &nbsp;  &nbsp;  &nbsp;  &nbsp;**Anaconda  安装 PyTorch** 不像其他的库一样直接 Pip 安装，而是到[PyTorch官网](https://pytorch.org/get-started/locally/)上选择你需要安装的环境配置，然后它会提供给你**单独的安装命令**。如下所示：
```cpp
conda install pytorch torchvision cudatoolkit=10.2 -c pytorch
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200705115723883.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

### 二. 为Anaconda添加国内镜像源
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在安装过程中，我们会遇到很多问题，很多需要下载的包可能会由于网络连接的关系下载失败，因此，我们需要为Anaconda添加国内的镜像源，如清华的镜像源。添加镜像源的命令如下：

```cpp
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
```
 &nbsp;  &nbsp;  &nbsp;  &nbsp;添加完镜像源后就可以**执行官网给出的安装命令了**。
```cpp
conda install pytorch torchvision cudatoolkit=10.2 -c pytorch
```
### 三. 手动下载缺失的库
 &nbsp;  &nbsp;  &nbsp;  &nbsp;**如果下载还是有问题的，你可以根据 cmd 命令窗口给出的错误提示，手动到清华镜像源的PyTorch库中下载对应的库到本地**，如下所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200705120135875.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;下载到本地后，用以下的命令进行本地的安装：
**注意， --use--local后的文件地址是本地的绝对地址。**

```cpp
conda install --use-local D:\downloads\torchvision-0.6.1-py37_cu102.tar.bz2
```
 &nbsp;  &nbsp;  &nbsp;  &nbsp;把缺失的库文件安装好后，**再次运行官网给出的安装命令**
```cpp
conda install pytorch torchvision cudatoolkit=10.2 -c pytorch
```
 &nbsp;  &nbsp;  &nbsp;  &nbsp;出现“done”就说明安装完成，可以进入Python 测试一下，导入torch成功！
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200705120459819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
！！​
