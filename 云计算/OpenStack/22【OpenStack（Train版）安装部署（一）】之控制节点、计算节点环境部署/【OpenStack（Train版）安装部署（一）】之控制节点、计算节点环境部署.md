@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## （一）	环境搭建
### 1.虚拟机设置
&nbsp;  &nbsp;  &nbsp;  &nbsp;两台虚拟机，一台是Controller节点，另一台是Compute节点，都采用minimal安装。

```cpp
root密码都为：wangdage980204
```

通过命令ip addr 得到ip：

```cpp
Controller：192.168.112.146
Compute：192.168.112.145
```
<br>


### 2.yum源设置为阿里云
&nbsp;  &nbsp;  &nbsp;  &nbsp;发现下载速度很快，且使用的就是阿里云的yum源，因此不需要更新yum源。
<br>


### 3.配置ntp时间同步（两个节点都需要）
&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，在需要配置文件修改的地方，我都将地址改为了Controller节点的IP地址。

安装时间同步服务

```cpp
[root@controller ~]# yum install chrony -y 
```
在时间同步服务的配置文件中添加子网段


```cpp
[root@controller ~]# vim /etc/chrony.conf 

allow 192.168.112.0/24 
```

设置时间同步服务自启动

```cpp
[root@controller ~]# systemctl restart chronyd.service 	#重启时间同步服务
[root@controller ~]# systemctl enable chronyd.service	#设置时间同步服务开机自启动
```
<br>



### 4.关闭防火墙selinux（两个节点都需要）
&nbsp;  &nbsp;  &nbsp;  &nbsp;关闭防火墙，关闭开机自启动防火墙

```cpp
systemctl stop firewalld
systemctl disable firewalld
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;永久关闭selinux

```cpp
#vi /etc/selinux/config
#SELINUX=enforcing改为SELINUX=disabled
重启生效
```
<br>


### 5.安装Train版的yum源（两个节点都需要）

```cpp
[root@controller ~]# yum install centos-release-openstack-train -y
```
<br>


## （二）Controller节点配置
### 1.安装客户端【controller节点】

```cpp
[root@controller ~]# yum install python-openstackclient -y
```
<br>


### 2.安装数据库【controller节点】 
&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，在需要配置文件修改的地方，我都将地址改为了Controller节点的IP地址。

```cpp
yum install mariadb mariadb-server python2-PyMySQL
```

设置配置文件

```cpp
cat > /etc/my.cnf.d/openstack.cnf << EOF

[mysqld] 
bind-address = 192.168.112.146 
default-storage-engine = innodb #默认存储引擎 
innodb_file_per_table = on #每张表独立表空间文件 
max_connections = 4096 #最大连接数 
collation-server = utf8_general_ci #默认字符集 
character-set-server = utf8 
EOF
```
设置mysql服务自启动
```cpp
systemctl enable mariadb.service	#开机自启动mysql
systemctl start mariadb.service		#开启mysql服务
```

进入mysql中

```cpp
mysql_secure_installation 
```

后面的操作就是

```cpp
回车--->n--->一路y
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;第一个回车是因为首次登陆，询问当前的密码，因为没有设置密码则直接按回车。

&nbsp;  &nbsp;  &nbsp;  &nbsp;n代表没有设置密码。
<br>


### 3.安装消息队列服务【controller节点】 
安装rabbitmq服务器

```cpp
[root@controller ~]# yum install rabbitmq-server -y	
```
配置rabbitmq服务自启动

```cpp
[root@controller ~]# systemctl enable rabbitmq-server.service #设置rabbitmq开机自启动
[root@controller ~]# systemctl start rabbitmq-server.service	  #开启rabbitmq服务
```

创建用户 openstack

```cpp
[root@controller ~]# rabbitmqctl add_user openstack RABBIT_PASS
Creating user "openstack"
```

授予权限 

```cpp
[root@controller ~]# rabbitmqctl set_permissions openstack ".*" ".*" ".*"
Setting permissions for user "openstack" in vhost "/"
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;通过netstat查询rabbitmq服务启动与否，可是Centos7中默认没有安装netstat，因此首先查询出netstat在本机中的包为net-tools，然后使用yum-y install net-tools 安装即可。


&nbsp;  &nbsp;  &nbsp;  &nbsp;通过netstat查询rabbitmq服务启动与否

```cpp
netstat -tnlup
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620163316128.png)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp;出现了5672端口和25672端口，则说明rabbbitmq安装成功。
<br>


### 4.安装memcache【controller节点】 
 安装memcache服务

```cpp
yum install memcached python-memcached 
```

```cpp
sed -i '/OPTIONS/c\OPTIONS="-l 0.0.0.0"' /etc/sysconfig/memcached
```
配置memcached服务自启动

```cpp
systemctl enable memcached.service 	#设置开机自启动
systemctl start memcached.service 		#开启服务
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;安装和启动好之后，同样使用netstat -tnlup查看端口情况，看到11211端口有程序在侦听则表示memcache安装成功

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620163428843.png)
<br>



### 5.安装etcd【controller节点】 
&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，在需要配置文件修改的地方，我都将地址改为了Controller节点的IP地址。

安装etcd服务

```cpp
yum install etcd
```

	
配置etcd的配置文件，注意IP地址都是Controller节点的IP地址：

```cpp
cp -a /etc/etcd/etcd.conf{,.bak}
cat > /etc/etcd/etcd.conf <<EOF
#[Member] 
ETCD_DATA_DIR="/var/lib/etcd/default.etcd" 		
ETCD_LISTEN_PEER_URLS="http://192.168.112.146:2380"	
ETCD_LISTEN_CLIENT_URLS="http://192.168.112.146:2379" 
ETCD_NAME="controller" #[Clustering] 	
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://192.168.112.146:2380" ETCD_ADVERTISE_CLIENT_URLS="http://192.168.112.146:2379" ETCD_INITIAL_CLUSTER="controller=http://192.168.112.146:2380" ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01" 
ETCD_INITIAL_CLUSTER_STATE="new" 
EOF
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，配置文件中不应该有其他的#注释，会导致文件解析失败。

配置etcd服务开机自启动

```cpp
systemctl enable etcd #开机自启动
systemctl start etcd 	#启动服务
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;安装和启动好之后，同样使用netstat -tnlup查看端口情况，看到2379和2380端口有程序在侦听则表示etcd安装成功
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620163648507.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

 
<br>



### 6.etcd介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp;etcd是CoreOS团队于2013年6月发起的开源项目，它的目标是构建一个高可用的分布式键值(key-value)数据库。etcd内部采用raft协议作为一致性算法，etcd基于Go语言实现。

&nbsp;  &nbsp;  &nbsp;  &nbsp;etcd作为服务发现系统，有以下的特点：
（1）简单：安装配置简单，而且提供了HTTP API进行交互，使用也很简单

（2）安全：支持SSL证书验证

（3）快速：根据官方提供的benchmark数据，单实例支持每秒2k+读操作

（4）可靠：采用raft算法，实现分布式系统数据的可用性和一致性






