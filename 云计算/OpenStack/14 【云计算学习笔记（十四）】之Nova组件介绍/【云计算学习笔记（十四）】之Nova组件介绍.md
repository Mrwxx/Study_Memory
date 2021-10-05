@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">



## 一．	Nova
### （一）	Nova组件说明
#### 1.	API组件介绍
##### （1）	nova-api Service
&nbsp;  &nbsp;  &nbsp;  &nbsp;这是暴露给用户请求的API接口，接受并响应终端用户的API请求，同时该API还支持Amazon EC2和特殊的管理特权API。
<br>


##### （2）	nova-api-metadata Service
&nbsp;  &nbsp;  &nbsp;  &nbsp;该API接收从实例元数据发来的请求，它应该安装在每台Nova节点上。
<br>


#### 2.	core组件介绍
##### （1）	nova-compute service
&nbsp;  &nbsp;  &nbsp;  &nbsp;一个守护进程，通过虚拟化层API接口创建和终止虚拟机实例，用户请求发送到Nova组件中，然后再向真正的虚拟化底层Xen或是KVM或是QEMU或是Vmware申请创建虚拟机实例。
<br>


##### （2）	nova-scheduled service
&nbsp;  &nbsp;  &nbsp;  &nbsp;当用户发送请求到Nova组件后，如果有多个Nova节点，那么由哪个Nova节点生成虚拟机需要进行调度，nova节点从nova调度器队列中获取虚拟机实例请求，进而生成虚拟机。
<br>


##### （3）	nova-conductor module
&nbsp;  &nbsp;  &nbsp;  &nbsp;该模块用于协调nova-compute服务和数据库之间交互数据，避免nova-compute服务直接访问数据库，注意，不要将该模块部署在nova-compute运行的节点之上。
<br>


#### 3.	network组件介绍
##### （1）	nova-network worker daemon
&nbsp;  &nbsp;  &nbsp;  &nbsp;接受来自于队列的网络任务和操控网络，用于操作nova节点上的网络情况。
<br>


##### （2）	nova-novncproxy daemon
&nbsp;  &nbsp;  &nbsp;  &nbsp;提供一个通过VNC连接来访问运行的虚拟机实例的代理，支持基于浏览器的novnc客户端。
<br>


##### （3）	nova-spicehtml5proxy daemon
&nbsp;  &nbsp;  &nbsp;  &nbsp;提供一个通过spice连接来访问运行的虚拟机实例的代理，支持基于H5客户端。
<br>


##### （4）	nova-xvpnvncproxy daemon
&nbsp;  &nbsp;  &nbsp;  &nbsp;提供一个通过VNC连接来访问运行的虚拟机实例的代理，支持Openstack-specific java客户端。
<br>


#### 4.	其他组件介绍
##### （1）	nova client
&nbsp;  &nbsp;  &nbsp;  &nbsp;nova的命令行工具，向nova服务发送请求的方式有两种，一种是通过Rest风格接口发送请求，另一种是通过nova命令行工具发送请求。
<br>


##### （2）	The queue
&nbsp;  &nbsp;  &nbsp;  &nbsp;在进程之间传递消息的中心，通常使用RabbitMQ。
<br>


##### （3）	SQL database
&nbsp;  &nbsp;  &nbsp;  &nbsp;数据库保存云基础设施建立和运行时的状态信息，通常采用的是Mysql。
<br>


### （二）	组件执行顺序介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	Rest接口的请求，nova命令行工具的请求或者是仪表盘界面的请求发送给nova-api；

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	如果是创建虚拟机的请求，则会发送给Scheduler调度器，选择一个compute节点来运行，该节点是管理整个虚拟化生命周期的节点，需要通过compute驱动来调用真正的虚拟化管理平台libvirt;

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	libvirt是redhat开发的虚拟化管理平台，底层管理着Xen，KVM，由它们来真正地创建，停止虚拟机。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210519150148829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

<br>


### （三）	Nova同其他组件的沟通
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	请求发送至KeyStone中进行校验，获取Nova节点的nova-api；

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	请求发送至nova-api中，写入nova-db，并且调用scheduler选择compute节点，配置nova-network网络情况，nova-volulme卷的情况，最后发送到nova-compute节点中，通过hypervisor虚拟机监视器创建；

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	compute节点与Glacne节点和Swift节点的交互都需要通过Keystone节点；
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021051915015887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
<br>



### （四）	虚拟机的启动流程
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	创建虚拟机的请求发送至nova-scheduler，如果已经指定了host，要在那一台机器上创建虚拟机，则该请求的优先级最大，直接发送请求到该compute节点中；

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	如果没有指定host，则需要通过计算权重来调度到最合适的计算节点中；

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	此时的每个compute节点中的libvirt虚拟机管理平台都会收集宿主机的资源情况，保存数据到数据库中，scheduler可以实时地从数据库中获取每个compute节点的资源情况；

