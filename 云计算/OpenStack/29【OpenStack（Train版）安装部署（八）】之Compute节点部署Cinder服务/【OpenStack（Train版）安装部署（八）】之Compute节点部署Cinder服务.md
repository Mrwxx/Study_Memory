@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 1. 创建cinder数据库并授权
&nbsp;  &nbsp;  &nbsp;  &nbsp;进入mysql中创建cinder数据库以及赋予该数据库的权限给用户cinder，并设置密码为CINDER_DBPASS。

```java
[root@controller ~]# mysql
MariaDB [(none)]> CREATE DATABASE cinder;
Query OK, 1 row affected (0.012 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'localhost' IDENTIFIED BY 'CINDER_DBPASS';
Query OK, 0 rows affected (0.001 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'%' IDENTIFIED BY 'CINDER_DBPASS';
Query OK, 0 rows affected (0.000 sec)
```
<br>




## 2.创建cinder用户
&nbsp;  &nbsp;  &nbsp;  &nbsp;在openStack中创建cinder用户：

```java
[root@controller ~]# openstack user create --domain default --password CINDER_PASS cinder
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/25e6acdea31d4d6f8155064d392ec6d7.png)

<br>



## 3.向cinder用户添加admin角色

```java
[root@controller ~]# openstack role add --project service --user cinder admin
```
<br>



## 4.创建cinderv2和cinderv3服务实体
&nbsp;  &nbsp;  &nbsp;  &nbsp;cinder有v2和v3两个并存版本的API，所以需要创建两个版本的service实例


```java
[root@controller ~]# openstack service create --name cinderv2 
--description "OpenStack Block Storage" volumev2
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/455de1512d34435ab20cd814c907d4d8.png)

 

```java
[root@controller ~]# openstack service create --name cinderv3 
--description "OpenStack Block Storage" volumev3
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a03f4f26c4ff4c829ead5b40b711f691.png)
<br>



 
## 5.创建块存储服务API端点
&nbsp;  &nbsp;  &nbsp;  &nbsp;给v2和v3版本的api创建endpoint


```java
[root@controller ~]# openstack endpoint create --region RegionOne volumev2 public http://192.168.112.146:8776/v2/%\(project_id\)s
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/a8d6d32721fe467ebe15a9085d2bdbc0.png)

 

```java
[root@controller ~]# openstack endpoint create --region RegionOne volumev2 internal http://192.168.112.146:8776/v2/%\(project_id\)s
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/30f9bb4007324bd8a65d53130cac6f60.png)

 

```java
[root@controller ~]# openstack endpoint create --region RegionOne volumev2 admin http://192.168.112.146:8776/v2/%\(project_id\)s
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/91398f0269c14e5e82fb2bb9d5e50e18.png)

 

```java
[root@controller ~]# openstack endpoint create --region RegionOne volumev3 public http://192.168.112.146:8776/v3/%\(project_id\)s
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/bdd0ee3f40d5406d9fcfc8c665da0441.png)

 

```java
[root@controller ~]# openstack endpoint create --region RegionOne volumev3 internal http://192.168.112.146:8776/v3/%\(project_id\)s
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c01261b9cc8345f2acbc4e27626cb471.png)

 

```java
[root@controller ~]# openstack endpoint create --region RegionOne volumev3 admin http://192.168.112.146:8776/v3/%\(project_id\)s
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b9fcdf41feff4a7081997954f0c93b86.png)

<br>



 

## 6.Controller节点安装cinder软件包并修改配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;安装openstack-cinder软件：

```java
yum install openstack-cinder
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;精简cinder.conf的配置文件：

```java
cp /etc/cinder/cinder.conf /etc/cinder/cinder.conf.bak
grep -Ev '#|^$' /etc/cinder/cinder.conf.bak>/etc/cinder/cinder.conf
```

```java
openstack-config --set /etc/cinder/cinder.conf   database connection mysql+pymysql://cinder:CINDER_DBPASS@192.168.112.146/cinder
openstack-config --set /etc/cinder/cinder.conf   DEFAULT  transport_url  rabbit://openstack:RABBIT_PASS@192.168.112.146
openstack-config --set /etc/cinder/cinder.conf   DEFAULT  auth_strategy  keystone
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   www_authenticate_uri  http://192.168.112.146:5000
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   auth_url  http://192.168.112.146:5000
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   memcached_servers  192.168.112.146:11211
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   auth_type  password
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   project_domain_name  default
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   user_domain_name  default
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   project_name  service
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   username  cinder
openstack-config --set /etc/cinder/cinder.conf   keystone_authtoken   password  CINDER_PASS
openstack-config --set /etc/cinder/cinder.conf   DEFAULT  my_ip  192.168.112.146
openstack-config --set /etc/cinder/cinder.conf   oslo_concurrency  lock_path  /var/lib/cinder/tmp
```
<br>




## 7.填充块存储数据库
&nbsp;  &nbsp;  &nbsp;  &nbsp;同步cinder数据库(填充块存储数据库)

```java
[root@controller ~]# su -s /bin/sh -c "cinder-manage db sync" cinder
```
<br>



## 8.配置计算节点的nova.conf来使用块存储
&nbsp;  &nbsp;  &nbsp;  &nbsp;在Compute节点中配置nova.conf文件，修改cinder选项为RegionOne：

```java
[root@compute01 ~]# vim /etc/nova/nova.conf
....
[cinder]
os_region_name = RegionOne
```
<br>



## 9.重新启动Compute API服务

```java
[root@controller ~]# systemctl restart openstack-nova-api.service
```
<br>



## 10.启动块存储服务，并设置开机自启

```java
[root@controller ~]# systemctl enable openstack-cinder-api.service openstack-cinder-scheduler.service
[root@controller ~]# systemctl start openstack-cinder-api.service openstack-cinder-scheduler.service
```
<br>



## 11.控制节点验证

```java
[root@controller ~]# cinder service-list
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b192f0728e0e418fbf6b0b38645728ea.png)
<br>



 
## 12.安装和配置存储节点【compute】
### （1）安装LVM软件包

```java
[root@compute01 ~]# yum install lvm2 device-mapper-persistent-data
```
<br>



### （2）启动LVM元数据服务，并设置开机自启

```java
[root@compute01 ~]# systemctl enable lvm2-lvmetad.service
[root@compute01 ~]# systemctl start lvm2-lvmetad.service
```
<br>



### （3）新增一个硬盘
&nbsp;  &nbsp;  &nbsp;  &nbsp;在vmware中新建一个20g的硬盘/dev/sdb，在compute节点中查看当前的硬盘情况：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/50bf260b2e194ed3904dd03a704eb1ec.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_15,color_FFFFFF,t_70,g_se,x_16)

<br>



### （4）创建LVM物理卷/dev/sdb；在这之前新增20G硬盘

```java
[root@compute01 ~]# pvcreate /dev/sdb
  Physical volume "/dev/sdb" successfully created.
```
<br>



### （5）创建LVM卷组cinder-volumes

```java
[root@compute01 ~]# vgcreate cinder-volumes /dev/sdb
  Volume group "cinder-volumes" successfully created
```
<br>



### （6）修改lvm配置文件(指定使用sdb磁盘）

```java
[root@computer ~]# vim /etc/lvm/lvm.conf
141行，取消，
#a表示允许，r表示拒绝 
#只允许lvm服务访问sdb中的数据，不允许lvm服务访问其他磁盘，这也间接实现了openstack创建的虚拟机只能访问sdb中的数据，不能访问其他磁盘
#设置只允许实例访问sdb逻辑卷中的数据；如果不配置的话，本机的其他服务也有可能会访问sdb逻辑卷中的数据
```

```java
filter = [ "a/sdb/", "r/.*/"]
```
<br>



### （7）Compute节点安装软件包

```java
yum install openstack-cinder targetcli python-keystone -y

yum install python-openstackclient -y
```
<br>



### （8）修改cinder配置文件

```java
[root@compute01 ~]# cp /etc/cinder/cinder.conf /etc/cinder/cinder.conf.bak
[root@compute01 ~]# grep -Ev '#|^$' /etc/cinder/cinder.conf.bak>/etc/cinder/cinder.conf
```


```java
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  database  connection mysql+pymysql://cinder:CINDER_DBPASS@192.168.112.146/cinder
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  DEFAULT transport_url rabbit://openstack:RABBIT_PASS@192.168.112.146
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  DEFAULT auth_strategy keystone
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  DEFAULT my_ip 192.168.112.145
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  DEFAULT enabled_backends lvm
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  DEFAULT glance_api_servers http://192.168.112.146:9292
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken www_authenticate_uri http://192.168.112.146:5000
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken auth_url http://192.168.112.146:5000
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken memcached_servers 192.168.112.146:11211
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken auth_type password
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken project_domain_name default
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken user_domain_name default
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken project_name service
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken username cinder
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  keystone_authtoken password CINDER_PASS
#指定LVM驱动程序；即通过指定的驱动创建LVM
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  lvm volume_driver cinder.volume.drivers.lvm.LVMVolumeDriver
#指定卷组(vg)
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  lvm volume_group cinder-volumes
#pv使用的是iscsi协议，可以提供块存储服务
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  lvm target_protocol iscsi
#iscsi管理工具
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  lvm target_helper lioadm
[root@c2 ~]# openstack-config --set /etc/cinder/cinder.conf  oslo_concurrency lock_path /var/lib/cinder/tmp
```
<br>



### （9）启动块存储卷服务及其相关，并设置开机自启

```java
[root@compute01 ~]# systemctl enable openstack-cinder-volume.service target.service
[root@compute01 ~]# systemctl start openstack-cinder-volume.service target.service
```
<br>



### （10）验证cinder块存储服务【controller节点】
查看卷的列表：

```java
[root@controller ~]# openstack volume service list
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/c1646cd8810a4fb2bb2fd2754df93091.png)
<br>




### （11）使用块存储服务向实例提供数据盘

创建一个20 GB的卷：


```java
[root@controller ~]# openstack volume create --size 20 volume1
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/8dd8351362ba4383b7f96256a044facd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_15,color_FFFFFF,t_70,g_se,x_16)

```java
[root@controller ~]# openstack volume list
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/dd81f13601bf4191a053cd07b8a7cf92.png)
<br>



### （12）将卷附加到实例

```java
openstack server add volume INSTANCE_NAME VOLUME_NAME
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;将volume1卷附加到firstVM实例：

```java
[root@controller ~]# openstack server add volume firstVM volume1
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;再次查看openstack中的volume状态可以看到volume1已经attach到了实例上

```java
[root@controller ~]# openstack volume list
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/17e4f68436b04ae3b40425baf5aa6f5f.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;通过Dashboard查看volume1的状态是可用，再通过界面的操作可以直接将volume1连接到实例firstVM中。想要验证该实例是否挂载了volume1，直接进入firstVM的控制台中，通过命令sudo fdisk -l命令可以查看到该实例挂载的卷。

![在这里插入图片描述](https://img-blog.csdnimg.cn/994fa3af38ff450b920d4c6fdcf0f781.png)


 















