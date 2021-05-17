@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	云计算变迁

### （一）	单主机多用户

 &nbsp;  &nbsp;  &nbsp;  &nbsp;资源相互依赖，相互影响，没有隔离。
<br>


### （二）	单主机多虚拟机

 &nbsp;  &nbsp;  &nbsp;  &nbsp;资源共享，互相隔离。

<br>

### （三）	多主机多虚拟机

 &nbsp;  &nbsp;  &nbsp;  &nbsp;资源调度和弹性扩展，互相隔离。

<br>


## 二．	云计算定义

 &nbsp;  &nbsp;  &nbsp;  &nbsp;云计算是一种按照使用量付费的模式，这种模式提供可用的，便捷的，按需的网络访问，进入可配置的计算资源共享池中（资源包括网络，服务器，存储，应用软件，服务），这些资源能够被快速地提供，只需要投入很少的管理工作。这也就是为什么云计算会分为IaaS，PaaS，SaaS等各种不同的类型，因为它们提供的都是不同的资源。
<br>



## 三．	云计算分类

### （一）	架构分类

#### 1.	私有云

 &nbsp;  &nbsp;  &nbsp;  &nbsp;公司内部定制的私有云，只供内部使用，因此安全性更高。
<br>


#### 2.	公有云

 &nbsp;  &nbsp;  &nbsp;  &nbsp;放在公网中，用户都能够访问到，容量大， 弹性扩容，成本较低。
<br>


#### 3.	混合云
 &nbsp;  &nbsp;  &nbsp;  &nbsp;公司内部的数据放在私有云中，公有云只存储与用户访问相关的数据，进而可以调用私有云中的服务，两种云混合在一起使用，既有大容量可扩容的特点，也保证了内部数据的安全性。
<br>


### （二）	供给方式分类

#### 1.	IaaS
 &nbsp;  &nbsp;  &nbsp;  &nbsp;基础设施即服务，将服务器，网络等基础设置作为服务提供给用户，用户获得了这些基础资源后，就可以在虚拟机上安装操作系统。如阿里云，腾讯云，AWS。
<br>


#### 2.	PaaS

 &nbsp;  &nbsp;  &nbsp;  &nbsp;平台即服务，不仅提供基础设施，还提供运行环境等，用户只需要安装软件即可。
<br>


#### 3.	SaaS

 &nbsp;  &nbsp;  &nbsp;  &nbsp;软件即服务，用户无需配置，只需要连接该服务，就可以使用该服务对应的软件功能，如微软的Office365，用户直接通过B/S的方式使用Office。

<br>


## 四．	OpenStack架构说明

 &nbsp;  &nbsp;  &nbsp;  &nbsp;这里采用的是第十版的OpenStack。
<br>


### （一）	Compute(Nova)计算服务

 &nbsp;  &nbsp;  &nbsp;  &nbsp;与虚拟化组件交互的核心，管理着整个流程。
<br>


### （二）	Image Service(Glance)镜像服务
 &nbsp;  &nbsp;  &nbsp;  &nbsp;管理镜像的组件。
<br>


### （三）	Object Storage(Swift)对象存储

 &nbsp;  &nbsp;  &nbsp;  &nbsp;分布式存储的组件。
<br>


### （四）	Block Storage(Cinder)块存储

 &nbsp;  &nbsp;  &nbsp;  &nbsp;提供块存储能力的组件。
<br>


### （五）	Networking(Neutron)网络服务

 &nbsp;  &nbsp;  &nbsp;  &nbsp;为所有的虚拟机提供网络服务。
<br>


### （六）	Dashboard(Horizon)仪表板
 &nbsp;  &nbsp;  &nbsp;  &nbsp;提供B/S架构的管理服务。

<br>

### （七）	Identity Service(Keystone)认证服务

 &nbsp;  &nbsp;  &nbsp;  &nbsp;所有组件的认证服务。
<br>


### （八）	Orchestration(Heat)编排
 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过yaml格式的文件来编排虚拟机创建的时间，配置等等参数。
<br>


### （九）	Telemetry(Ceiloometer)监控

 &nbsp;  &nbsp;  &nbsp;  &nbsp;监控虚拟机消耗的资源，实现按量付费功能。
<br>


### （十）	Database Service(Trove)数据库服务

 &nbsp;  &nbsp;  &nbsp;  &nbsp;提供与数据库交互的能力。
<br>


### （十一） Data Processing(Sahara)数据处理

 &nbsp;  &nbsp;  &nbsp;  &nbsp;提供与Hadoop结合提供数据处理的能力。
<br>



## 五．	安装配置

### （一）	基础配置

```cpp
操作系统：CentOS7;
OpenStack版本：JUNO
关闭防火墙和SELinux；
```
<br>


### （二）	节点配置
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Controller控制节点：安装Keystone, Trove, Glance服务等比较简单的服务；
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Compute计算节点：Nova服务；
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Network节点：Neutron服务；
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Block节点：Cinder块存储服务；

<br>


### （三）	网络配置
 &nbsp;  &nbsp;  &nbsp;  &nbsp;有三种网络，第一种是管理网络，各个组件之间的通信采用管理网络；第二个是实例网络，各个虚拟机之间的通信采用的是实例网络；第三种是外部网络，提供虚拟机访问外网的能力。对于IP的配置上，如下所示：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;Controller节点：一块网卡用于管理网络中，地址是192.168.222.5;
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Compute节点：两块网卡，一块用于管理网络中，为192.168.222.10; 一块用于实例网路中，为172.16.0.10;
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Network节点：三块网卡，一块用于管理网络中，为192.168.222.6；一块用于实例网络中，IP为172.16.0.6；一块用于外部网络中，IP为100.100.100.10；
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Block节点：一块网卡，用于管理网络中，为192.168.222.20;
<br>


### （四）	节点资源配置
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Controller节点： 2核CPU，1.5GB内存，1块网卡，100GB硬盘，因为其中有Glance镜像服务;
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Compute节点：最大的CPU，最大的内存，2块网卡，100GB硬盘；
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Network节点：2核CPU，1.5GB内存，3块网卡，20GB硬盘；
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Block节点：2核CPU，1GB内存，1块网卡，20GB硬盘用于系统盘安装，100GB硬盘用于块存储


