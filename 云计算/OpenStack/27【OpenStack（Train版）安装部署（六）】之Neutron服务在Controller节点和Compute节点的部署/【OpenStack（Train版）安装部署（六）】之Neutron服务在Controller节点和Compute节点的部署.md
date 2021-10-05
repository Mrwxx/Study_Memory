@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## （一）	Controller节点安装Neutron服务
### 1.创建neutron数据库并授权 （Controller节点）

```cpp
[root@controller ~]# mysql -uroot 
```

```cpp
MariaDB [(none)]> CREATE DATABASE neutron; 
Query OK, 1 row affected (0.000 sec) 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; neutron数据库的登录密码是

```cpp
NEUTRON_DBPASS
MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost'  IDENTIFIED BY 'NEUTRON_DBPASS'; 
Query OK, 0 rows affected (0.001 sec) 
```


```cpp
MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' IDENTIFIED BY 'NEUTRON_DBPASS'; 
Query OK, 0 rows affected (0.000 sec)
```
<br>


### 2. 创建neutron用户 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建openstack用户neutron，它的domain是default，密码是NEUTRON_PASS，用于在keystone做认证

```cpp
[root@controller ~]# openstack user create --domain default --password NEUTRON_PASS neutron
```
<br>


### 3. 向neutron用户添加admin角色 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 角色是admin，所在的项目是service

```cpp
[root@controller ~]# openstack role add --project service --user neutron admin
```
<br>


### 4. 创建neutron服务实体 
&nbsp;  &nbsp;  &nbsp;  &nbsp; neutron服务实体，类型为network

```cpp
[root@controller ~]# openstack service create --name neutron --description "OpenStack Networking" network
```
<br>


### 5. 创建neutron服务端点 

```cpp
[root@controller ~]# openstack endpoint create --region RegionOne network public http://192.168.112.146:9696

[root@controller ~]# openstack endpoint create --region RegionOne network internal http://192.168.112.146:9696

[root@controller ~]# openstack endpoint create --region RegionOne network admin http://192.168.112.146:9696
```
<br>


### 6. 安装软件包{配置二层网络} 

```cpp
[root@controller ~]# yum install openstack-neutron openstack-neutron-ml2 openstack-neutron-linuxbridge ebtables conntrack-tools -y 
```

说明： 
&nbsp;  &nbsp;  &nbsp;  &nbsp; openstack-neutron：neutron-server的包 

&nbsp;  &nbsp;  &nbsp;  &nbsp; openstack-neutron-ml2：ML2 plugin的包 

&nbsp;  &nbsp;  &nbsp;  &nbsp; openstack-neutron-linuxbridge：linux bridge network provider相关的包 

&nbsp;  &nbsp;  &nbsp;  &nbsp; ebtables：防火墙相关的包，配置iptables规则

<br>


### 7. 修改 neutron 配置文件 
#### （1）neutron server的配置文件neutron.conf 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简配置neutron.conf配置文件

```cpp
[root@controller ~]# cp -a /etc/neutron/neutron.conf{,.bak} 
[root@controller ~]# grep -Ev '^$|#' /etc/neutron/neutron.conf.bak > /etc/neutron/neutron.conf 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置mysql连接地址

```cpp
openstack-config --set /etc/neutron/neutron.conf database connection mysql+pymysql://neutron:NEUTRON_DBPASS@192.168.112.146/neutron 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置二层网络core-plugin为ML2

```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT core_plugin ml2 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置三层网络插件service-plugin为router

```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT service_plugins router
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置rabbitmq连接

```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT transport_url rabbit://openstack:RABBIT_PASS@192.168.112.146 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置认证方式为keystone

```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT auth_strategy keystone 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 当网络接口发生变化时，通知给计算节点	

```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT notify_nova_on_port_status_changes true 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 当端口数据发生变化，通知计算节点

```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT notify_nova_on_port_data_changes true 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置keystone的认证地址url

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken www_authenticate_uri http://192.168.112.146:5000 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置keystone的认证地址uri

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken auth_url http://192.168.112.146:5000
```

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; Memcached服务器地址

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken memcached_servers 192.168.112.146:11211 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 认证方式为password

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken auth_type password 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 项目所属Domain为default

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken project_domain_name default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 用户所属Domain为default

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken user_domain_name default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 认证所属项目service

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken project_name service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 认证的用户名为neutron

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken username neutron 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 认证的密码：NEUTRON_PASS

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken password NEUTRON_PASS 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置锁的路径

```cpp
openstack-config --set /etc/neutron/neutron.conf oslo_concurrency lock_path /var/lib/neutron/tmp 
```

neutron需要给nova返回数据：
&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova的认证地址：

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  auth_url http://192.168.112.146:5000
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova 的认证方式

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  auth_type password
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova的项目所属Domain为default

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  project_domain_name default
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova的用户所属Domain为default

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  user_domain_name default
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova的Region名称

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  region_name RegionOne
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova的项目名为service

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  project_name service
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova的用户名为nova

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  username nova
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置nova的密码为NOVA_PASS

```cpp
openstack-config --set  /etc/neutron/neutron.conf nova  password NOVA_PASS
```

<br>


#### （2）ML2 plugin的配置文件ml2_conf.ini 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简ml2_conf.ini配置文件：

```cpp
[root@controller ~]# cp -a /etc/neutron/plugins/ml2/ml2_conf.ini{,.bak} 
[root@controller ~]# grep -Ev '^$|#' /etc/neutron/plugins/ml2/ml2_conf.ini.bak > /etc/neutron/plugins/ml2/ml2_conf.ini 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置驱动类型；单一扁平网络(桥接)和vlan；让二层网络支持桥接，支持基于vlan做子网划分

```cpp
openstack-config --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2 type_drivers flat,vlan,vxlan
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 租户网络类型

```cpp
openstack-config --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2 tenant_network_types vxlan
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 启用Linuxbridge和l2机制，(l2population机制是为了简化网络通信拓扑，减少网络广播)：

```cpp
openstack-config --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2 mechanism_drivers linuxbridge,l2population
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 启用端口安全扩展驱动程序，基于iptables实现访问控制；但配置了扩展安全组会导致一些端口限制，造成一些服务无法启动

```cpp
openstack-config --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2 extension_drivers port_security 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置公共虚拟网络为flat网络

```cpp
openstack-config --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2_type_flat flat_networks provider 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 为私有网络配置VXLAN网络识别的网络范围

```cpp
openstack-config --set /etc/neutron/plugins/ml2/ml2_conf.ini ml2_type_vxlan vni_ranges 1:1000
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 启用 ipset 增加安全组的方便性

```cpp
openstack-config --set /etc/neutron/plugins/ml2/ml2_conf.ini securitygroup enable_ipset true
```
<br>


#### （3）linux bridge network provider的配置文件linuxbridge_agent.ini 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简配置文件linuxbridge_agent.ini：

```cpp
[root@controller ~]# cp -a /etc/neutron/plugins/ml2/linuxbridge_agent.ini{,.bak} 
[root@controller ~]# grep -Ev '^$|#' /etc/neutron/plugins/ml2/linuxbridge_agent.ini.bak > /etc/neutron/plugins/ml2/linuxbridge_agent.ini 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置物理网卡的映射，provider表示该节点可用的物理网络名字(physical network, 名字可以随便定义)，physical_interface_mappings用来把名字和该网络使用的物理网卡对应起来。
&nbsp;  &nbsp;  &nbsp;  &nbsp; 后面的就是该名称对应的物理网卡。

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini linux_bridge physical_interface_mappings provider:ens33
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 允许用户创建自定义网络(3层网络)

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini vxlan enable_vxlan true
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 启用安全组并配置 Linux 桥接 iptables 防火墙驱动

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini securitygroup enable_security_group true 
```

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini securitygroup firewall_driver neutron.agent.linux.iptables_firewall.IptablesFirewallDriver 
```


&nbsp;  &nbsp;  &nbsp;  &nbsp; 控制节点IP地址

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini vxlan local_ip 192.168.112.146		
```

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini vxlan l2_population true
```
<br>


### 8.修改内核参数

```cpp
echo 'net.bridge.bridge-nf-call-iptables=1' >> /etc/sysctl.conf
echo 'net.bridge.bridge-nf-call-ip6tables=1' >> /etc/sysctl.conf
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 表示向内核加入参数

```cpp
modprobe br_netfilter	
```

```cpp
sysctl -p
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628182239156.png)

<br>


### 9.配置Linuxbridge接口驱动和外部网络网桥

&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简l3_agent.ini配置文件

```cpp
cp -a /etc/neutron/l3_agent.ini{,.bak}
grep -Ev '^$|#' /etc/neutron/l3_agent.ini.bak > /etc/neutron/l3_agent.ini
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 接口驱动为linuxbridge

```cpp
openstack-config --set /etc/neutron/l3_agent.ini DEFAULT interface_driver linuxbridge
```
<br>


### 10.修改dhcp_agent 配置文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简dhcp_agent.ini配置文件：

```cpp
 cp -a /etc/neutron/dhcp_agent.ini{,.bak}
 grep -Ev '^$|#' /etc/neutron/dhcp_agent.ini.bak > /etc/neutron/dhcp_agent.ini 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定默认接口驱动为linux网桥

```cpp
 openstack-config --set /etc/neutron/dhcp_agent.ini DEFAULT interface_driver linuxbridge
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定DHCP驱动

```cpp
 openstack-config --set /etc/neutron/dhcp_agent.ini DEFAULT dhcp_driver neutron.agent.linux.dhcp.Dnsmasq
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 开启iso元数据

```cpp
 openstack-config --set /etc/neutron/dhcp_agent.ini DEFAULT enable_isolated_metadata true
```
<br>


### 11. 配置元数据代理，以便和nova通讯 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简配置文件：

```cpp
[root@controller ~]# cp -a /etc/neutron/metadata_agent.ini{,.bak} 
[root@controller ~]# grep -Ev '^$|#' /etc/neutron/metadata_agent.ini.bak > /etc/neutron/metadata_agent.ini 
```


```cpp
openstack-config --set /etc/neutron/metadata_agent.ini DEFAULT nova_metadata_host controller 
```

```cpp
openstack-config --set /etc/neutron/metadata_agent.ini DEFAULT metadata_proxy_shared_secret METADATA_SECRET
```
<br>



### 12.修改nova配置文件，用于neutron交互
&nbsp;  &nbsp;  &nbsp;  &nbsp; 修改nova.conf配置文件，添加neutron的信息，便于与neutron的交互：

```cpp
openstack-config --set /etc/nova/nova.conf neutron url http://192.168.112.146:9696
openstack-config --set /etc/nova/nova.conf neutron auth_url http://192.168.112.146:5000
openstack-config --set /etc/nova/nova.conf neutron auth_type password
openstack-config --set /etc/nova/nova.conf neutron project_domain_name default
openstack-config --set /etc/nova/nova.conf neutron user_domain_name default
openstack-config --set /etc/nova/nova.conf neutron region_name RegionOne
openstack-config --set /etc/nova/nova.conf neutron project_name service
openstack-config --set /etc/nova/nova.conf neutron username neutron
openstack-config --set /etc/nova/nova.conf neutron password NEUTRON_PASS
openstack-config --set /etc/nova/nova.conf neutron service_metadata_proxy true
openstack-config --set /etc/nova/nova.conf neutron metadata_proxy_shared_secret METADATA_SECRET
```
<br>


### 13.创建ML2插件文件符号连接

&nbsp;  &nbsp;  &nbsp;  &nbsp; 网络服务初始化脚本需要/etc/neutron/plugin.ini指向ML2插件配置文件的符号链接

```cpp
ln -s /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugin.ini
```
<br>


### 14.初始化数据库

```cpp
 su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf 
--config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron
```

<br>

### 15.重启计算节点nova-api服务

```cpp
systemctl restart openstack-nova-api.service
```
<br>



### 16.开启neutron服务、设置开机自启动
&nbsp;  &nbsp;  &nbsp;  &nbsp; 开机自启动下面的四个neutron子服务：

```cpp
[root@ct ~]# systemctl enable neutron-server.service \
neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
neutron-metadata-agent.service
[root@ct ~]# systemctl start neutron-server.service \
neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
neutron-metadata-agent.service
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 检查neutron服务的启动情况，
 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628182315654.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 因为配置了第三层L3网络服务、所以需要启动第三层服务

```cpp
[root@ct ~]# systemctl enable neutron-l3-agent.service
[root@ct ~]# systemctl restart neutron-l3-agent.service
```
<br>


## （二）Compute节点安装neutron服务
### 1. 安装组件 

&nbsp;  &nbsp;  &nbsp;  &nbsp; ipset：iptables的扩展，允许匹配规则的集合而不仅仅是一个IP

```cpp
[root@compute01 ~]# yum install openstack-neutron-linuxbridge ebtables ipset conntrack-tools -y
```
<br>


### 2. 修改配置文件 
#### （1）修改neutron主配置文件 
&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简neutron.conf配置文件

```cpp
[root@compute01 ~]# cp -a /etc/neutron/neutron.conf{,.bak} 
[root@compute01 ~]# grep -Ev '^$|#' /etc/neutron/neutron.conf.bak > /etc/neutron/neutron.conf 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT transport_url rabbit://openstack:RABBIT_PASS@192.168.112.146
```

```cpp
openstack-config --set /etc/neutron/neutron.conf DEFAULT auth_strategy keystone 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken www_authenticate_uri http://192.168.112.146:5000 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken auth_url http://192.168.112.146:5000 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken memcached_servers 192.168.112.146:11211 
```

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken auth_type password 
```

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken project_domain_name default 
```

```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken user_domain_name default 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken project_name service 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken username neutron 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf keystone_authtoken password NEUTRON_PASS 
```


```cpp
openstack-config --set /etc/neutron/neutron.conf oslo_concurrency lock_path /var/lib/neutron/tmp
```
<br>


#### （2）配置Linux网桥代理linuxbridge_agent.ini
&nbsp;  &nbsp;  &nbsp;  &nbsp; 精简配置文件：

```cpp
cp -a /etc/neutron/plugins/ml2/linuxbridge_agent.ini{,.bak}
grep -Ev '^$|#' /etc/neutron/plugins/ml2/linuxbridge_agent.ini.bak > /etc/neutron/plugins/ml2/linuxbridge_agent.ini
```

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini linux_bridge physical_interface_mappings  provider:ens33
```


```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini vxlan enable_vxlan  true
```

Compute节点的IP

```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini vxlan local_ip 192.168.112.145
```


```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini vxlan l2_population true
```


```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini securitygroup enable_security_group  true
```


```cpp
openstack-config --set /etc/neutron/plugins/ml2/linuxbridge_agent.ini securitygroup firewall_driver  neutron.agent.linux.iptables_firewall.IptablesFirewallDriver
```
<br>


#### （3）修改内核
允许虚拟机的数据通过物理机出去

```cpp
echo 'net.bridge.bridge-nf-call-iptables=1' >> /etc/sysctl.conf		
echo 'net.bridge.bridge-nf-call-ip6tables=1' >> /etc/sysctl.conf
```
modprobe：用于向内核中加载模块，或者从内核中移除模块。modprobe -r 表示移除

```cpp
modprobe br_netfilter		
```

```cpp
sysctl -p
```
<br>


### 3. 修改nova.conf配置文件

```cpp
openstack-config --set /etc/nova/nova.conf neutron auth_url http://192.168.112.146:5000
openstack-config --set /etc/nova/nova.conf neutron auth_type password
openstack-config --set /etc/nova/nova.conf neutron project_domain_name default
openstack-config --set /etc/nova/nova.conf neutron user_domain_name default
openstack-config --set /etc/nova/nova.conf neutron region_name RegionOne
openstack-config --set /etc/nova/nova.conf neutron project_name service
openstack-config --set /etc/nova/nova.conf neutron username neutron
openstack-config --set /etc/nova/nova.conf neutron password NEUTRON_PASS
```
<br>


### 4. 重启nova计算服务 

```cpp
systemctl restart openstack-nova-compute.service
```
<br>


### 5. 启动neutron服务和设置开机自启动 

```cpp
systemctl enable neutron-linuxbridge-agent.service 
systemctl start neutron-linuxbridge-agent.service
```
<br>


## （三）Controller节点中验证Neutron服务的安装

```cpp
[root@controller ~]# openstack extension list --network
```


```cpp
[root@controller ~]# openstack network agent list
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628182331815.png)


或者从内核中移除模块。modprobe -r 表示移除可以看到，Controller节点中有4个Neutron子服务，Compute节点中有一个Neutron子服务，State都是Up。
<br>


## （四）总结部署思路
配置neutron组件的用户、认证、endpoint
设置提供者provider网络（这里是桥接模式）
① 配置二层网络
② 配置网桥（插件）
③ 优化内核
④ 配置网桥接口与外部对接
⑤ 修改DHCP配置（修改配置文件、代理）
⑥ 配置网桥与内部组件的配置（修改配置文件、代理）
设置neutron与nova对接的配置



























