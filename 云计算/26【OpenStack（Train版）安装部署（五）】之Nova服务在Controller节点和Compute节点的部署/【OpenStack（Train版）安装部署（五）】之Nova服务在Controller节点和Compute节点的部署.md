@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## （一）Nova子服务节点分配
&nbsp;  &nbsp;  &nbsp;  &nbsp; 计算服务nova较之前的服务稍显复杂（但没有网络服务neutron复杂），它需要在控制节点和计算节点都安装。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 控制节点主要安装nova-api（nova主服务）、nova-scheduler（nova调度服务）、nova-conductor（nova数据库服务，提供数据库访问）、nova-novncproxy（nova的vnc服务，提供实例的控制台）等服务。
计算节点主要安装nova-compute（nova计算服务）。

## （二）Nova子服务Controller节点部署
### 1. 创建数据库并授权
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建三个数据库nova, nova_api, nova_cell0，并授予nova用户对三个数据库的本地登录以及远程登录权限，并设定密码为NOVA_DBPASS：

```cpp
MariaDB [(none)]> CREATE DATABASE nova_api; 
Query OK, 1 row affected (0.000 sec) 

MariaDB [(none)]> CREATE DATABASE nova; 
Query OK, 1 row affected (0.000 sec)

MariaDB [(none)]> CREATE DATABASE nova_cell0; 
Query OK, 1 row affected (0.000 sec) 

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' IDENTIFIED BY 'NOVA_DBPASS'; 
Query OK, 0 rows affected (0.000 sec) 

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' IDENTIFIED BY 'NOVA_DBPASS'; 
Query OK, 0 rows affected (0.000 sec) 

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' IDENTIFIED BY 'NOVA_DBPASS'; 
Query OK, 0 rows affected (0.000 sec) 

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' IDENTIFIED BY 'NOVA_DBPASS'; 
Query OK, 0 rows affected (0.000 sec) 

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'localhost' IDENTIFIED BY 'NOVA_DBPASS'; 
Query OK, 0 rows affected (0.000 sec) 

MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' IDENTIFIED BY 'NOVA_DBPASS'; 
Query OK, 0 rows affected (0.001 sec)
```

### 2. 创建nova用户 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建OpenStack的用户nova，密码为NOVA_PASS：

```cpp
[root@controller ~]# openstack user create --domain default --password NOVA_PASS nova
```

### 3. 向nova用户添加admin角色 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 为nova用户添加admin角色，并且加入项目service中：

```cpp
[root@controller ~]# openstack role add --project service --user nova admin
```

### 4. 创建nova服务实体 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建nova服务service，类型是compute：

```cpp
[root@controller ~]# openstack service create --name nova --description "OpenStack Compute" compute
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的service列表：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175430788.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

 
### 5. 给Nova服务关联endpoint（端点）
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建三个endpoint端点，与之前一样：

```cpp
[root@controller ~]# openstack endpoint create --region RegionOne compute public http://192.168.112.146:8774/v2.1

[root@controller ~]# openstack endpoint create --region RegionOne compute internal http://192.168.112.146:8774/v2.1

[root@controller ~]# openstack endpoint create --region RegionOne compute admin http://192.168.112.146:8774/v2.1
```

### 6.Controller节点需要安装的nova子服务
&nbsp;  &nbsp;  &nbsp;  &nbsp; nova-api, nova-conductor, nova-novncproxy, nova-scheduler四个组件：

```cpp
[root@controller ~]# yum install openstack-nova-api openstack-nova-conductor openstack-nova-novncproxy openstack-nova-scheduler -y
```

### 7. 修改nova.conf配置文件 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简nova.conf配置文件：

```cpp
cp -a /etc/nova/nova.conf{,.bak} 
grep -Ev '^$|#' /etc/nova/nova.conf.bak > /etc/nova/nova.conf
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定支持的API类型

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT enabled_apis osapi_compute,metadata 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 定义Controller节点的IP

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT my_ip 192.168.112.146
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过neutron获取IP地址

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT use_neutron true 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 防火墙驱动

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT firewall_driver nova.virt.firewall.NoopFirewallDriver 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定连接的rabbitmq

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT transport_url rabbit://openstack:RABBIT_PASS@192.168.112.146 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; nova_api数据库的mysql连接

```cpp
openstack-config --set /etc/nova/nova.conf api_database connection mysql+pymysql://nova:NOVA_DBPASS@192.168.112.146/nova_api
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; nova数据库的mysql连接

```cpp
openstack-config --set /etc/nova/nova.conf database connection mysql+pymysql://nova:NOVA_DBPASS@192.168.112.146/nova 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement数据库的mysql连接

```cpp
openstack-config --set /etc/nova/nova.conf placement_database connection mysql+pymysql://placement:PLACEMENT_DBPASS@192.168.112.146/placement 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; nova的认证策略为keystone

```cpp
openstack-config --set /etc/nova/nova.conf api auth_strategy keystone 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的认证url

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken auth_url http://192.168.112.146:5000/v3
```

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; memcached的服务地址

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken memcached_servers 192.168.112.146:11211 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone认证方式为password

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken auth_type password 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的项目Domain为Default

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken project_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 用户的Damain为Default

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken user_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 项目名为service

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken project_name service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的登录用户名为nova

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken username nova 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; nova用户在keystone的登录密码为NOVA_PASS

```cpp
openstack-config --set /etc/nova/nova.conf keystone_authtoken password NOVA_PASS 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; vnc如果配置不正确，则连接不上虚拟机的控制台

```cpp
openstack-config --set /etc/nova/nova.conf vnc enabled true
```

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; vnc监听地址

```cpp
openstack-config --set /etc/nova/nova.conf vnc server_listen ' $my_ip' 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; server的客户端地址为本机地址；此地址是管理网的地址

```cpp
openstack-config --set /etc/nova/nova.conf vnc server_proxyclient_address ' $my_ip' 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; glance的地址

```cpp
openstack-config --set /etc/nova/nova.conf glance api_servers http://192.168.112.146:9292 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定锁路径, 锁的作用是创建虚拟机时，在执行某个操作的时候，需要等此步骤执行完后才能执行下一个步骤，不能并行执行，保证操作是一步一步的执行

```cpp
openstack-config --set /etc/nova/nova.conf oslo_concurrency lock_path /var/lib/nova/tmp 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的region名称

```cpp
openstack-config --set /etc/nova/nova.conf placement region_name RegionOne 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的项目所属Domain为Default

```cpp
openstack-config --set /etc/nova/nova.conf placement project_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的项目名为service

```cpp
openstack-config --set /etc/nova/nova.conf placement project_name service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的认证方式为password

```cpp
openstack-config --set /etc/nova/nova.conf placement auth_type password 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的用户所属Domain为Default

```cpp
openstack-config --set /etc/nova/nova.conf placement user_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的认证url

```cpp
openstack-config --set /etc/nova/nova.conf placement auth_url http://192.168.112.146:5000/v3 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的用户名为placement

```cpp
openstack-config --set /etc/nova/nova.conf placement username placement 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的密码为PLACEMENT_PASS

```cpp
openstack-config --set /etc/nova/nova.conf placement password PLACEMENT_PASS
```

### 8. 填充nova-api数据库
&nbsp;  &nbsp;  &nbsp;  &nbsp; 初始化nova_api数据库

```cpp
[root@controller ~]# su -s /bin/sh -c "nova-manage api_db sync" nova 
```

### 9.注册cell0数据库
&nbsp;  &nbsp;  &nbsp;  &nbsp; nova服务内部把资源划分到不同的cell中，把计算节点划分到不同的cell中；openstack内部基于cell把计算节点进行逻辑上的分组

```cpp
[root@controller ~]# su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建cell1单元格

```cpp
[root@controller ~]# su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 初始化nova数据库，可以通过 /var/log/nova/nova-manage.log 日志判断是否初始化成功

```cpp
[root@controller ~]# su -s /bin/sh -c "nova-manage db sync" nova
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可使用以下命令验证cell0和cell1是否注册成功

```cpp
su -s /bin/sh -c "nova-manage cell_v2 list_cells" nova 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 验证cell0和cell1组件是否注册成功
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175455269.png)

### 10. 启动计算服务nova并将其配置为开机自启
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将Controller节点的4个nova子服务设置开机自启动

```cpp
[root@controller ~]# systemctl enable openstack-nova-api.service openstack-nova-scheduler.service openstack-nova-conductor.service openstack-nova-novncproxy.service 
[root@controller ~]# systemctl start openstack-nova-api.service openstack-nova-scheduler.service openstack-nova-conductor.service openstack-nova-novncproxy.service 
```

### 11.检测nova服务启动情况

&nbsp;  &nbsp;  &nbsp;  &nbsp; 同样，使用netstat -tnlup查看端口情况，如出现8774和8775端口则表示nova服务正常启动。
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175503916.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 再通过访问Controller节点的8774端口，返回信息：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175508930.png)

## （三）Nova子服务Compute节点部署
### 1.安装OpenStack Train版本源

```cpp
[root@compute01 ~]# yum install centos-release-openstack-train -y
```

### 2.安装nova-compute软件包

```cpp
[root@compute01 ~]# yum install openstack-nova-compute -y
```

### 3.修改计算节点的nova配置文件 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简nova.conf配置文件

```cpp
[root@compute01 ~]# cp -a /etc/nova/nova.conf{,.bak} 
[root@compute01 ~]# grep -Ev '^$|#' /etc/nova/nova.conf.bak > /etc/nova/nova.conf
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 安装openstack-utils工具

```cpp
[root@controller ~]# yum install -y openstack-utils
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定支持的API类型

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT enabled_apis osapi_compute,metadata 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 定义Compute节点的IP

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT my_ip 192.168.112.145
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过neutron获取IP地址

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT use_neutron true 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 防火墙驱动

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT firewall_driver nova.virt.firewall.NoopFirewallDriver 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定连接的rabbitmq，在Controller节点中

```cpp
openstack-config --set /etc/nova/nova.conf DEFAULT transport_url rabbit://openstack:RABBIT_PASS@192.168.112.146
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 认证策略为keystone

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf api auth_strategy keystone 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone认证URL（Controller中）

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken auth_url http://192.168.112.146:5000/v3 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; memcached服务地址（Controller中）

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken memcached_servers 192.168.112.146:11211 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的认证方式为password

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken auth_type password 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的项目Damain名为Default

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken project_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的用户Damain名为Default

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken user_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的project为service

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken project_name service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的用户名为nova

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken username nova 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; keystone的认证密码为NOVA_PASS

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf keystone_authtoken password NOVA_PASS 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; vnc连接控制台

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf vnc enabled true 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; vnc服务监听IP

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf vnc server_listen 0.0.0.0 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; vnc代理客户端地址为本机地址

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf vnc server_proxyclient_address ' $my_ip' 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; vnc代理基本url（Controller中）

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf vnc novncproxy_base_url http://192.168.112.146:6080/vnc_auto.html 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; glance的api

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf glance api_servers http://192.168.112.146:9292 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程锁位置：

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf oslo_concurrency lock_path /var/lib/nova/tmp 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的Region名称

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement region_name RegionOne 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的project所属的Domain为Default

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement project_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的project名为service

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement project_name service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的认证方式为password

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement auth_type password 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的用户Domain为Default

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement user_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的认证地址url

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement auth_url http://192.168.112.146:5000/v3 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的用户名为placement

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement username placement 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; placement服务的密码

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf placement password PLACEMENT_PASS 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; compute子服务的libvirt类型

```cpp
[root@compute01 ~]# openstack-config --set /etc/nova/nova.conf libvirt virt_type qemu
```

### 4. 确定计算节点是否支持虚拟机硬件加速 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 检查当前计算节点：

```cpp
[root@compute01 ~]# egrep -c '(vmx|svm)' /proc/cpuinfo
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175531635.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果此命令返回值不是0，则计算节点支持硬件加速，不需要加入下面的配置。 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果此命令返回值是0，则计算节点不支持硬件加速，并且必须配置libvirt为使用QEMU而不是KVM，需要编辑/etc/nova/nova.conf 文件中的[libvirt]部分： `[libvirt] virt_type = qemu`

### 5.启动Compute服务及其相关服务，并设置开机自启 

```cpp
[root@compute01 ~]# systemctl enable libvirtd.service openstack-nova-compute.service 
[root@compute01 ~]# systemctl start libvirtd.service openstack-nova-compute.service
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 执行命令后，openstack-nova-compute.service服务一直启动不起来，到/var/
log/nova/nova-compute.log日志中查看启动失败情况，结果如下：
 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，是rabbitmq的连接问题，此时我并没有开启Controller节点的rabbitmq，因此尝试开启Controller节点。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175543393.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可是openstack-nova-compute.service服务依然启动失败，查看compute节点的nova-compute.log日志可以看到如下信息：

```cpp
tail /var/log/nova/nova-compute.log
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175550757.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 从最后可以看到，是AccessRefused导致的，即AMQPLAIN拒绝登录，说明是登录rabbitmq失败，因此到Controller节点中的查看rabbitmq的日志：

```cpp
tail /var/log/rabbitmq/rabbitt@controller.log
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175601983.png)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 从中间的一行可以看到，是openstack用户没有有效的认证，下面查看rabbitmq中的用户信息：

```cpp
rabbitmqctl list_users
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175608728.png)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，只有一个guest用户，权限是admin，因此需要添加一个openstack用户，设置密码为RABBIT_PASS，这个密码是我们在Compute节点的nova.conf中配置的密码：

```cpp
rabbitmqctl add_user openstack RABBIT_PASS
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 增加配置、读取及写入相关权限

```cpp
rabbitmqctl set_permissions openstack ".*" ".*" ".*"
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 重启Controller节点中的rabbitmq服务，

```cpp
systemctl restart rabbitmq-server.service
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 重启Compute节点中的openstack-nova-compute服务

```cpp
systemctl restart openstack-nova-compute.service
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 最终，查看openstack-nova-compute服务启动成功，如下所示：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/202106261756203.png)

### 6.修改Compute节点主机名配置 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 修改主机名，方便操作

```cpp
hostnamectl set-hostname computer
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置hosts

```cpp
192.168.112.145 compute
```

### 7.验证（controller节点操作）
&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看compute节点是否注册到controller上，通过消息队列；需要在controller节点执行以下的指令：

```cpp
[root@ct ~]# openstack compute service list --service nova-compute
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175633126.png)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 这里有两个是因为我在之前没有设置Compute节点的hostname，修改完hostname之后的状态为UP。

### 8.发现计算节点主机（Controller节点操作）
&nbsp;  &nbsp;  &nbsp;  &nbsp; 扫描当前openstack中有哪些计算节点可用，发现后会把计算节点创建到cell中，后面就可以在cell中创建虚拟机；相当于openstack内部对计算节点进行分组，把计算节点分配到不同的cell中 

```cpp
[root@ct ~]# su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，发现两个cell，一个是cell1，其中不包含host注解；另一个是cell，其中包含了两个host，就是我们之前在未设置hostname和设置之后的两个host：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175642304.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于在以后添加新的计算节点时，必须在控制器节点上运行”su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova“以注册这些新的计算节点。这样很麻烦，因此可以修改Controller节点的nova.conf文件：

```cpp
[root@ct ~]# vim /etc/nova/nova.conf
[scheduler]
discover_hosts_in_cells_interval = 300			#每300秒扫描一次
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置扫描时间为300秒扫描一次。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 重启Controller节点的nova-api.service服务：

```cpp
systemctl restart openstack-nova-api.service
```

### 9.验证计算节点服务（Controller节点操作）
&nbsp;  &nbsp;  &nbsp;  &nbsp; 检查 nova 的各个服务是否都是正常，以及 compute 服务是否注册成功

```cpp
[root@ct ~]# openstack compute service list
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175656193.png)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，controller节点中启动了两个服务，compute节点启动了一个服务。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看OpenStack各个组件的 访问api 是否正常

```cpp
[root@ct ~]# openstack catalog list
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175706162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看cell的api和placement的api是否正常，只要其中一个有误，后期无法创建虚拟机

```cpp
[root@ct ~]# nova-status upgrade check
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626175714388.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)








