## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	IAAS，PAAS，SAAS

### （一）	传统项目搭建流程

1.	部署组网

2.	配置存储

3.	配置硬件

4.	配置虚拟化

5.	配置服务器操作系统

6.	部署数据库

7.	部署安全措施

8.	部署运行环境

9.	运行程序
	
<br>


### （二）	IAAS(Infrastructure as a Service)
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 基础设施即服务，IAAS供应商已经将组网，存储，硬件，服务器操作系统虚拟化了，我们直接在IAAS平台上部署数据库，运行环境，运行程序即可。像阿里云，华为云这种买来的服务器都是IAAS平台，直接在上面部署程序。

1.	部署数据库

2.	部署安全措施

3.	部署运行环境

4.	运行程序

<br>



### （三）	PAAS（Platform as a Service）

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 平台即服务，我们从PAAS供应商那里获取的已经安装好运行环境了，直接在上面运行程序即可，Docker就是一种PAAS。

1.	运行程序。

<br>


### （四）	SaaS(Software as a Service)
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 软件即服务，连运行的程序都不需要安装，整个平台已经安装好了硬件与软件，我们直接调用相应的服务即可。

<br>



## 二．	Docker的由来

 &nbsp;  &nbsp;  &nbsp;  &nbsp; Docker是由Dotcloud公司开源的一款产品，主要基于PAAS（平台即服务）平台为开发者提供服务。它的底层技术是Linux Container，内核虚拟化技术，可以提供轻量级的虚拟化，隔离进程和资源，Linux Container使用比较麻烦，Docker作为Linux Container技术的高级容器引擎，即管理器，能够非常方便地管理虚拟资源。

 &nbsp;  &nbsp;  &nbsp;  &nbsp; Docker基于go语言开发，遵从Apache2.0协议开源，能够提供不同运行环境的隔离，每个运行环境可以由不同的软件通过标准化的手段自由组装而成，

<br>


## 三．	Docker与传统虚拟化的对比

### （一）	传统的虚拟化
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 如寄居架构的虚拟化，服务器上运行着主机的操作系统，再上一层是Hypervisor，其上是不同的虚拟机运行着不同的程序。在这种架构中，不同的应用程序是通过不同的虚拟机操作系统的内核来隔离的。
<br>



### （二）	Docker
 &nbsp;  &nbsp;  &nbsp;  &nbsp; Docker架构中，依然有服务器，服务器上运行着主机的操作系统，其上是Docker引擎，再上面就是不同的运行环境了，不同的运行环境是隔离的，每个环境中运行着各自的应用程序。与传统的虚拟化相比，省去了虚拟机的操作系统的资源消耗，因此应用程序可以获得更多的资源。但是容器的安全性较差，因为不同共享的是同一块内核，只能做到容器级别的隔离。而传统的虚拟化的隔离是通过不同的操作系统内核实现的，安全性更强。

<br>



## 四．	Docker的构成

### （一）	Docker Client
 &nbsp;  &nbsp;  &nbsp;  &nbsp; Docker的客户端，用于向Docker Server发送请求。
<br>



### （二）	Docker Server
 &nbsp;  &nbsp;  &nbsp;  &nbsp; Docker Server会作为守护进程运行着，是Docker daemon的主要组成部分。接受用户通过Docker Client发送的请求，还可以按照相应的路由规则实现路由分发，接受远程的请求。
<br>



### （三）	Docker镜像

 &nbsp;  &nbsp;  &nbsp;  &nbsp; Docker镜像是封装好的运行环境，当Docker镜像加载完成后变成了容器，因此容器就是正在运行的运行环境，当容器停止后可以转换为镜像。
<br>



### （四）	Docker仓库
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 仓库存储着镜像，镜像可以上传到仓库中。官方的仓库是https://hub.docker.com

<br>



## 五．	Docker组件的协同方式

### （一）	拉取镜像
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 首先Docker Client通过docker pull xx发送命令给Docker daemon，他会在本机中搜索有无这个镜像，如果没有向远程仓库中发送请求下载镜像。
<br>



### （二）	生成容器
 &nbsp;  &nbsp;  &nbsp;  &nbsp; Docker client通过docker run xx命令发送给Docker Daemon，该镜像生成容器在本地中，可通过docker stop xx停止容器。

<br>



## 六．	Docker架构的技巧

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 镜像只要我们不去改动，永远是不会坏掉的，因为真正运行的是由镜像加载来的容器，我们在容器上安装不同的程序，即使容器坏掉，我们也能够根据镜像再次加载新的容器。

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 为了减少不同镜像的存储压力，镜像的结构是分层的，根据镜像之间的依赖关系，是由不同的子镜像所组成的，当一个镜像的子镜像已经存在时，可以不用拉取，直接使用本地的子镜像即可，大大节省了镜像的存储空间。

