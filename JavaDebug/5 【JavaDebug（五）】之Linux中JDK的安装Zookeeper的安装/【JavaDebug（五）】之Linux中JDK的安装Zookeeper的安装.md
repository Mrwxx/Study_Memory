## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	Linux中JDK的安装
### （一）	下载Linux环境下的jdk1.8
[云盘链接](https://pan.baidu.com/s/1ykSB3dsaYR1ow1iaU3FScg&shfl=sharepset)
<br>


### （二）	JDK解压
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将JDK压缩包上传到Linux服务器中，一般是放到/usr/local这个目录下，使用解压命令将JDK压缩包解压到/usr/local目录下。

```cpp
[root@localhost local]# tar -zxvf jdk-8u181-linux-x64.tar.gz -C /usr/local
```
<br>



### （三）	配置JDK环境变量
&nbsp;  &nbsp;  &nbsp;  &nbsp; /etc/profile文件涉及到Linux系统的环境变量，所以我们需要配置JDK，这样CIA可以在任意的目录下访问JDK。

打开 /et/cprofile文件

```cpp
vim /etc/profile
```

在文件尾部添加以下的内容：

```cpp
#JDK的安装目录
export JAVA_HOME=/usr/local/jdk1.8.0_161
 
export JRE_HOME=${JAVA_HOME}/jre
 
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
 
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
 
export PATH=$PATH:${JAVA_PATH}
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 按ESC键并输入 :wq保存退出后，再通过命令 source /etc/profile 使profile文件生效。

<br>


### （四）	测试JDK的安装
&nbsp;  &nbsp;  &nbsp;  &nbsp; 键入命令`javac` 或者 `java -version`，查看是否有输出。


<br>



## 二．Zookeeper的安装

&nbsp;  &nbsp;  &nbsp;  &nbsp; 1.	从官网下载Zookeeper的压缩包之后，将压缩包上传到linux系统中。
<br>




&nbsp;  &nbsp;  &nbsp;  &nbsp; 2.	解压缩压缩包

```cpp
tar -zxvf zookeeper-3.4.6.tar.gz
```

<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 3.	进入zookeeper-3.4.6目录，创建data目录，作为数据目录

```cpp
mkdir data
```
<br>




&nbsp;  &nbsp;  &nbsp;  &nbsp; 4.	进入conf目录，把zoo_sample.cfg改名为zoo,cfg

```cpp
cd conf
mv zoo_sample.cfg zoo.cfg
```

<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 5.	打开zoo.cfg文件，修改data属性为刚创建的data目录路径
dataDir=/usr/local/zookeeper-3.4.6/data


