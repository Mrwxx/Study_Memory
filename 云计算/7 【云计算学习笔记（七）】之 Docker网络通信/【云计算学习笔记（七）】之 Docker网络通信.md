@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Docker网络通信

### （一）	概述

&nbsp;  &nbsp;  &nbsp;  &nbsp;Docker网络通信主要解决三个问题，容器与容器之间的通信，容器访问外部网络，外部网络访问容器。
<br>


### （二）	容器之间的通信
&nbsp;  &nbsp;  &nbsp;  &nbsp;首先宿主机本身有一个网卡，在安装完Docker之后，会出现一个docker0，可以将它理解为一个网桥交换机，用于交换不同容器之间的数据，同时创建的还有不同的网络命名空间（Network Namespace），不同的命名空间是隔离的，且这个命名空间有两端，一端固定在容器的内部成为虚拟网卡， 另一端在宿主机中生成一个vethxxxxx，每多一个容器就会多一个vethxxxx。该交换机实现了容器之间的通信与隔离以及容器与宿主机之间的通信问题。

<br>

### （三）	容器与外部网络的通信

#### 1.	容器访问外部网络

&nbsp;  &nbsp;  &nbsp;  &nbsp;使用SNAT规则访问外部网络，如下所示：

```cpp
iptables -t nat -A POSTROUTING -s 172.17.0.0/16 -o docker0 -j MASQUERADE
```
<br>


#### 2.	外部网络访问容器
&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们启动如下的docker命令时，映射端口给宿主机，就会出现两条DNAT规则

```cpp
docker run -d -p 80:80 apache
```

```cpp
iptables -t nat -A PREROUTING -m addrtype -dst-type LOCAL -j DOCKER
iptables -t nat -A DOCKER ! -I docker0 -p tcp -m tcp -dport 80 -j DNAT -to-destination 172.17.0.2:80
```
<br>


### （四）	namespace类型

&nbsp;  &nbsp;  &nbsp;  &nbsp;前面使用Network Namespace实现了容器之间网络的隔离，其实namespace还有很多类型，可以实现多种资源的隔离：
<br>


#### 1.	UTS
&nbsp;  &nbsp;  &nbsp;  &nbsp;隔离主机名和域名。
<br>


#### 2.	IPC
&nbsp;  &nbsp;  &nbsp;  &nbsp;隔离信号量，消息队列和共享内存。
<br>


#### 3.	PID
&nbsp;  &nbsp;  &nbsp;  &nbsp;隔离进程编号。

<br>

#### 4.	NetWork
&nbsp;  &nbsp;  &nbsp;  &nbsp;隔离网络设备，网络栈，端口。
<br>


#### 5.	Mount
&nbsp;  &nbsp;  &nbsp;  &nbsp;隔离挂载点（文件系统）。
<br>


#### 6.	User
&nbsp;  &nbsp;  &nbsp;  &nbsp;隔离用户和用户组（内核版本要求3.8）。
<br>


### （五）	Docker网络模式的修改

#### 1.	Docker进程网络修改

&nbsp;  &nbsp;  &nbsp;  &nbsp;Docker进程网络修改相当于Docker主程序的修改，后面的容器都会相应地进行修改。
<br>


##### （1）	-b,--bridge = “”

&nbsp;  &nbsp;  &nbsp;  &nbsp;指定Docker使用的网桥设备，由于默认Docker会自动创建和使用docker0网桥设备，通过此参数可以使用已经存在的网桥设备。
<br>


##### （2）	--bip
&nbsp;  &nbsp;  &nbsp;  &nbsp;指定Docker0的IP和掩码，使用标准的CIDR形式，如10.10.10.10/24，这个用于防止容器的IP冲突问题。

<br>

##### （3）	--dns

&nbsp;  &nbsp;  &nbsp;  &nbsp;配置容器的DNS服务，在启动Docker进程时添加，所有容器都会生效。
<br>


#### 2.	容器网络修改

##### （1）	--dns

&nbsp;  &nbsp;  &nbsp;  &nbsp;指定启动的容器的DNS
<br>


##### （2）	--net

&nbsp;  &nbsp;  &nbsp;  &nbsp;指定容器的网络通讯方式，有以下四个值：

&nbsp;  &nbsp;  &nbsp;  &nbsp;bridge: Docker 默认方式，网桥模式，隔离化的容器间数据通信；

&nbsp;  &nbsp;  &nbsp;  &nbsp;none: 容器没有网络栈，只有回环网卡，即容器没有网络，用于特殊容器，如专门做计算的容器不需要网络，提高容器安全性；

&nbsp;  &nbsp;  &nbsp;  &nbsp;container: 使用其他容器的网络栈，Docker容器会加入其他容器的network namespace中，即直接与其他容器共用网络；

&nbsp;  &nbsp;  &nbsp;  &nbsp;host: 容器使用Host的网络，没有自己独立的网络栈，容器可以完全访问Host的网络，即容器将自己完全暴露给Host，Host可以直接访问容器中的服务，非常危险；
<br>


#### 3.	网桥模式下暴露端口

##### （1）	-p :容器端口

&nbsp;  &nbsp;  &nbsp;  &nbsp;将指定的容器端口映射到主机所有地址的一个动态端口；
<br>


##### （2）	-p 主机端口:容器端口

&nbsp;  &nbsp;  &nbsp;  &nbsp;将指定容器端口映射到指定的主机端口；
<br>


##### （3）	-p 主机IP::容器端口

&nbsp;  &nbsp;  &nbsp;  &nbsp;将指定容器端口映射到指定主机的动态端口；
<br>


##### （4）	-p 主机IP:主机端口:容器端口
&nbsp;  &nbsp;  &nbsp;  &nbsp;将指定容器端口映射到指定主机的指定端口；
<br>


##### （5）	-P（大写）
&nbsp;  &nbsp;  &nbsp;  &nbsp;暴露所需要的所有端口给主机；

<br>

#### 4.	查看容器当前映射关系
使用如下命令：

```cpp
docker port ContainerName
```
<br>


#### 5.	修改docker0网桥的配置

&nbsp;  &nbsp;  &nbsp;  &nbsp;在/etc/docker/daemon.json文件中存储的就是docker0网桥的配置，我们可以对它进行修改。

<br>

### （六）	常见隔离方式

#### 1.	基础命令

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看当前可用的网络空间

```cpp
docker network ls
```

```cpp
docker network create -d 类型 网络空间名称
```

类型有 
&nbsp;  &nbsp;  &nbsp;  &nbsp;overlay network: 使不同主机的容器实现通信；
&nbsp;  &nbsp;  &nbsp;  &nbsp;bridge network: 网桥通信

如：

```cpp
docker network create -d bridge lamp
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建了一个名为lamp的网桥命名空间

<br>

#### 2.	通过网络命名空间隔离容器
&nbsp;  &nbsp;  &nbsp;  &nbsp;要使用该网络命名空间，可在运行容器时设置  --network=lamp即可，表示使用lamp这个网络命名空间。如果我们要让两个容器之间相互隔离，就让它们运行在不同的网络命名空间中即可。

