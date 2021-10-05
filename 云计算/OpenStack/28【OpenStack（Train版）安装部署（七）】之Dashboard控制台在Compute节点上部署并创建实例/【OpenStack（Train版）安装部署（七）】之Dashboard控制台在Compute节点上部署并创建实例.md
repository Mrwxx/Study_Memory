@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">



## （一）Horizon服务
### 1.概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; OpenStack仪表板Dashboard服务的项目名称是Horizon，它所需的唯一服务是身份服务keystone，开发语言是python的web框架Django。
<br>



### 2.语言环境 

```cpp
Python 2.7、3.6或3.7
Django 1.11、2.0和2.2 
Django 2.0和2.2支持在Train版本中处于试验阶段。 
Ussuri发行版（Train发行版之后的下一个发行版）将使用Django 2.2作为主要的Django版本。Django 2.0支持将被删除。 
```
<br>



### 3.可访问的keystone endpoint 
&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的endpoint是可以访问的。

<br>


### 4.其他服务都是可选的
&nbsp;  &nbsp;  &nbsp;  &nbsp; 从Stein版本开始，Horizon支持以下服务： 

```java
cinder：块状存储 
glance：镜像管理 
neutron：网络 
nova：计算 
swift：对象存储 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果已配置好服务keystone的endpoint，那么Horizon将对其进行检测并自动启用其支持。 Horizon还通过插件支持许多其他OpenStack服务。 在
<br>



## （二）Horizon服务安装
### 1.安装节点选择
&nbsp;  &nbsp;  &nbsp;  &nbsp; 计算节点（compute）上安装仪表板服务horizon 由于horizon运行需要apache，为了不影响控制节点上的keystone等其他服务使用的apache，故在计算节点上安装。安装之前确认以前安装的服务是否正常启动。
<br>



### 2.软件安装
安装dashboard服务

```java
[root@compute ~]# yum install openstack-dashboard -y
```

安装httpd服务

```java
yum -y install httpd
```

设置httpd服务的自动启动和开始：

```java
systemctl enable httpd.service
systemctl start httpd.service
```
<br>



### 3. 修改local_setting本地控制台的配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 进入 /etc/openstack-dashboard/目录下，查看其中的配置文件：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/b108d8fe896b43099fd863a1dcd6bc1b.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 修改/etc/openstack-dashboard/local_settings本地控制台的配置文件：

```java
#修改的内容如下：
#修改local_setting本地控制台的配置文件
#使用Python导入一个模块
import os								
from django.utils.translation import ugettext_lazy as _
from openstack_dashboard.settings import HORIZON_CONFIG
#不开启调式	
DEBUG = False							
#只允许通过列表中指定的域名访问dashboard；允许通过指定的IP地址及域名#访问dahsboard；['*']表示允许所有域名
ALLOWED_HOSTS = ['*']			       
LOCAL_PATH = '/tmp'
#这是一个自定义的key，自动生成的。当多个dashboard实例分布在不同的机器上（通常在#一个负载均衡器）。
#你必须确保会话获取所有请求路由到相同的dashboard实例或你为它们设置相同的secret_key。
SECRET_KEY='9665a3cff2428daec89c'

#指定session引擎
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'		

#指定memcache地址及端口
CACHES = {							
    'default': {
         'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
         'LOCATION': '192.168.112.146:11211',	
    }
}

#以下配置session信息存放到memcache中；session信息不仅可以存放到memcache中，也可以存放到其他地方
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'	

#Controller节点IP
OPENSTACK_HOST = "192.168.112.146"						

#Keystone的URL
OPENSTACK_KEYSTONE_URL = "http://%s:5000/v3" % OPENSTACK_HOST	

#让dashboard支持域
OPENSTACK_KEYSTONE_MULTIDOMAIN_SUPPORT = True			

#配置openstack的API版本
OPENSTACK_API_VERSIONS = {
    "identity": 3,
    "image": 2,
    "volume": 3,
}

#Keystone的默认Domain为Default
OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default"

#keystone的默认角色为user
OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"

#neutron网络配置
OPENSTACK_NEUTRON_NETWORK = {					
    'enable_auto_allocated_network': False,
    'enable_distributed_router': False,
    'enable_fip_topology_check': False,
    'enable_ha_router': False,
    'enable_lb': False,
    'enable_firewall': False,
    'enable_vpn': False,
    'enable_ipv6': True,
    'enable_quotas': True,
    'enable_rbac_policy': True,
    'enable_router': True,
    'default_dns_nameservers': [],
    'supported_provider_types': ['*'],
    'segmentation_id_range': {},
    'extra_provider_types': {},
    'supported_vnic_types': ['*'],
    'physical_networks': [],
}
#定义使用的网络类型，[*]表示
TIME_ZONE = "Asia/Shanghai"					
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 以下两步官方文档中没有，但是需要执行，否则dashboard打不开或显示不对 
<br>



### 4.重建apache的dashboard配置文件

&nbsp;  &nbsp;  &nbsp;  &nbsp; 重新生成openstack-dashboard.conf 

```java
cd /usr/share/openstack-dashboard 
python manage.py make_web_conf --apache > /etc/httpd/conf.d/openstack-dashboard.conf
```
<br>



### 5.备用机制 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 若出现不能正常访问，请操作以下步骤： 建立策略文件（policy.json）的软链接，否则登录到dashboard将出现权限错误和显示混乱 

```java
ln -s /etc/openstack-dashboard /usr/share/openstack-dashboard/openstack_dashboard/conf
```
<br>



### 6.重启Apache服务
&nbsp;  &nbsp;  &nbsp;  &nbsp; ( 由于dashborad会重新复制代码文件，重启apache会比较慢 ）

```java
[root@compute ~]# systemctl enable httpd.service 
[root@compute01 ~]# systemctl restart httpd.service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于dashboard的运行机制是把网站下的所有文件删除之后再重新复制，所以重启httpd需要等待一段时间。
<br>




### 7.重新启动控制节点（controller）上的memcache服务 

```java
[root@controller ~]# systemctl restart memcached.service
```
<br>



## （三）验证Horizon服务
&nbsp;  &nbsp;  &nbsp;  &nbsp; 连接到Compute节点的IP：192.168.112.145
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/4f405a6c92954aad903fbaaebbef6fbf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 在登录页面依次填写：“域：default、用户名：admin、密码:ADMIN_PASS”（在~.bashrc中已定义）；完成后，进行登陆。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/c475c3a9d41e4cd58736e9602367e3ee.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_14,color_FFFFFF,t_70,g_se,x_16)

<br>



## （四）DashBoard中创建实例测试
### 1、检查各个节点间的网络通讯 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在控制节点执行ping 

```java
ping compute
ping 192.168.112.145
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ccf4556df61d4dd2818d7e81d275e4ea.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，Controller节点和Compute节点通信没有问题，由于Linux中的ping命令会默认持续进行，通过Ctrl + Z 暂停放入后台。然后通过jobs -l查询使用的进程号，再通过kill -9 杀死指定的进程。
<br>



### 2、删除NetworkManager软件包 （Controller和Compute）
在控制节点和计算节点都执行 

```java
yum remove NetworkManager -y
```
<br>



### 3. controller节点创建网络

```java
neutron net-create --shared --provider:physical_network provider 
--provider:network_type flat WAN
```

参数说明：

```java
--share 指明所有项目都可以使用这个网络，否则只有创建者能使用
--external 指明是外部网络
--provider:physical-network provider，指明物理网络的提供者，与下面neutron的配置文件对应，
其中provider是标签，可以更改为其他，但是2个地方必须要统一。
[ml2_type_flat]
flat_networks = provider

--provider:network_type flat，指明这里创建的网络是flat类型，
即实例连接到此网络时和物理网络是在同一个网段，无vlan等功能。
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/31bf590c5bda4a1c94193dc844343b71.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

 <br>



### 4. controller节点创建子网

```java
neutron subnet-create --name subnet-wan --allocation-pool 
start=192.168.112.200,end=192.168.112.230 --dns-nameserver 223.5.5.5 
--gateway 192.168.112.254 WAN 192.168.112.0/24
```

参数说明：

```java
--network 指明父网络
--allocation-pool start=192.168.112.200,end=192.168.112.230 指明子网起始地址和终止地址
--dns-nameserver 指明dns服务器
--gateway 指明网关地址
--subnet-range 指明子网网段
vm-subnetwork 子网名称
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/135991f18fbb4798bd29a2fb57dea2e9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)
<br>




### 5. 检查网络配置
在控制节点执行以下操作
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/b0c3bc9bb03b4b39b4bc9180b33d5cea.png)
<br>



### 6. 控制节点创建硬件配置方案

```java
openstack flavor create --id 0 --vcpus 1 --ram 64 --disk 1 m1.nano
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c9213d72493f43428241e0123b117351.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

 
<br>



### 7. dashboard创建实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 直接在dashboard界面中直接点击创建实例来创建实例，配置镜像，网络，硬件配置。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/bea67e8b24244848a7289e308eb5d67d.png)

<br>



### 8.连接实例控制台

&nbsp;  &nbsp;  &nbsp;  &nbsp; 启动实例后，进入控制台可以直接进入该实例虚机中。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/90b25d177f2449f6bd2f10a16486a76c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)








