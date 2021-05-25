@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	Glance
### （一）	Glance的服务功能
1.	Glance镜像服务使用户可以发现，注册并检索虚拟机镜像文件。

2.	Glance镜像服务提供了一个REST API接口，使用户可以查询虚拟机镜像的元数据和检索一个实际的镜像文件，元数据指的是对象的大小和类型。

3.	虚拟机镜像可以存储在简单的文件系统或者作为OpenStack对象存储。

4.	默认情况下，上传的虚拟机镜像的存储路径为/var/lib/glance/images/
<br>


### （二）	Glance的组件
#### 1.glance-api
&nbsp;  &nbsp;  &nbsp;  &nbsp;一个用来接收镜像的发现，检索和存储的API接口，该接口面向用户，用户通过该API接口进行查询。
<br>


#### 2.glance-registry
&nbsp;  &nbsp;  &nbsp;  &nbsp;用来存储，处理和检索镜像的元数据，元数据包括对象的大小和类型，它是一个OpenStack镜像服务之间使用的内部服务，不能暴露给用户使用。
<br>


#### 3. Database
&nbsp;  &nbsp;  &nbsp;  &nbsp;支持的数据库，用来存储镜像的元数据的大小和类型，一般的数据库都支持，如MySql或SqlLite。
<br>


#### 4.	Storage repository for image files
&nbsp;  &nbsp;  &nbsp;  &nbsp;镜像文件的存储仓库，支持各种存储类型，如对象存储，块存储，HTTP，但是有些存储只支持只读访问，如HTTP远程访问只允许读取，不允许修改镜像文件。
<br>


### （三）	Glance的基本概念
#### 1.	Image Identifiers
&nbsp;  &nbsp;  &nbsp;  &nbsp;镜像的定位符，是全局唯一的，格式是<Glance Server Location>/images/<ID>，前面是Glance服务器的地址，中间的images是固定格式，最后的ID是镜像的ID。
<br>


#### 2.	Image Status
&nbsp;  &nbsp;  &nbsp;  &nbsp;镜像的状态，有5种状态，如下所示：

```cpp
Queued：镜像ID被保留，镜像还没有上传；
Saving：镜像正在被上传；
Active：镜像可以被使用了；
Killed：镜像损坏或者不可用；
Deleted：镜像被删除了；
```
<br>


#### 3.	Disk Format
&nbsp;  &nbsp;  &nbsp;  &nbsp;镜像的磁盘格式，如下所示：

```cpp
Raw：以二进制方式存储的，优点是访问速度非常快，缺点是不支持动态地扩容；
Vhd：微软提出的格式，Vmware，Xen，VirtualBox都支持；
Vmdk：VMware提出的格式，已成为统一的标准；
Vdi：VirtualBox提出的格式；
ISO：基本的镜像类型；
Qcow2：QEMU支持；
Aki：Amazon对Kernel镜像的格式；
Ari：Amazon对ramdisk镜像的格式；
Ami：Amazon对machine镜像的格式；
```
<br>


#### 4.	Container Format
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们不仅需要磁盘的格式，还需要为镜像提供隔离的容器格式，有如下的容器格式：

```cpp
Bare：不支持动态扩容；
Ovf：Open Virtual Format，标准格式，支持动态扩容，还支持导出导入；
Aki，Ami，Ari都是Amazon提供的容器格式；
```
<br>


### （四）	Glance组件工作流
&nbsp;  &nbsp;  &nbsp;  &nbsp;用户通过Rest风格的API访问Glance-api，Glance-api与Glance-registry元数据交互，元数据直接和数据库交互获取元数据，Glance-api也可以直接和镜像文件系统交互。

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517161821442.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)



