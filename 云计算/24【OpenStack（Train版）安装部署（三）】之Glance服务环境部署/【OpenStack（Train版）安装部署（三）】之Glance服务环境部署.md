@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">



## （一）Glance服务部署
### 1.创建数据库并授权 
&nbsp;  &nbsp;  &nbsp;  &nbsp;登录mysql

```cpp
[root@controller ~]# mysql -u root 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建数据库glance

```cpp
MariaDB [(none)]> CREATE DATABASE glance; 
Query OK, 1 row affected (0.000 sec) 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;为glance用户本地命令行以及远程登录赋予权限，用户密码为GLANCE_DBPASS

```cpp
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'GLANCE_DBPASS'; 
Query OK, 0 rows affected (0.000 sec) 
```

```cpp
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'GLANCE_DBPASS'; 
Query OK, 0 rows affected (0.000 sec)
```

### 2.glance关于keystone的操作
&nbsp;  &nbsp;  &nbsp;  &nbsp;首先要建立一个glance用户，然后创建一个项目，最后关联赋予角色。在搭建keystone的时候，我们已经创建了一个组件共有的项目service项目，glance属于一个组件，keystone属于一个组件，nova属于一个组件等等的这些所有的组件都在一个项目中(service项目)，所以项目就不需要建立，直接赋予角色信息。

&nbsp;  &nbsp;  &nbsp;  &nbsp;对于glance来说，它属于管理着镜像服务，keystone管理着认证服务，neutron管理着网络服务，nova，cinder等等，这些所有的管理者的用户必须是admin。

&nbsp;  &nbsp;  &nbsp;  &nbsp;为glance的使用者创建用户glance，密码为GLANCE_PASS

```cpp
openstack user create --domain default --password GLANCE_PASS glance 
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170138391.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;对glance-service添加admin角色,提示：用户glance只有在一个确定的项目service内才有角色的概念，单独的用户或者单独的项目都是无法绑定角色的，因此将glance用户绑定到项目service中。

```cpp
openstack role add --project service --user glance admin 
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170146284.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，为glance用户添加了admin角色后，glance用户的后缀为ec07c2，同时它的project也是service项目。
### 3.创建glance服务实体
创建glance服务的catalog：service+endpoint

创建glance服务实体 

```cpp
[root@controller ~]# openstack service create --name glance --description "OpenStack Image" image
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，一定要指定它的类型为image镜像类型

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170155899.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170202343.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，当前有两个service，一个是keystone的，一个是glance，两个service的Type都不一样。

### 4. 创建镜像服务API访问端点
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建外部访问glance的API

```cpp
[root@controller ~]# openstack endpoint create --region RegionOne image public http://192.168.112.146:9292
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170212955.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建内部访问glance的API

```cpp
[root@controller ~]# openstack endpoint create --region RegionOne image internal http://192.168.112.146:9292
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170221712.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建admin访问glance的API

```cpp
[root@controller ~]# openstack endpoint create --region RegionOne image admin http://192.168.112.146:9292
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170228322.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看所有的endpoint，可以看到glance服务的三种API访问方式：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170236573.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意现在的这些操作都是在keystone里面，还没有具体的glance服务。
&nbsp;  &nbsp;  &nbsp;  &nbsp;keystone对glance的操作就结束了 接下来就是正式安装glance了
### 5. 安装glance软件包 

```cpp
[root@controller ~]# yum install openstack-glance
```

### 6.配置glance
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）一定要在opesntack-glance-api.service服务启动之前部署好存储设备，因为该服务在启动时会加载存储驱动检索存储设备，如果事先不存在，就意味着该服务没有识别到任何可用的存储设备，即便是后来你又新增了存储，仍然是无效的，最终导致你上传镜像失败；
&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）一定要赋予opesntack-glance-api.service服务对存储设备的可写权限。

&nbsp;  &nbsp;  &nbsp;  &nbsp;配置文件中的存储设备为/var/lib/glance/images，因此创建该目录，修改权限

```cpp
mkdir /var/lib/glance/images
chown -R glance.glance /var/lib/glance/images
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;首先精简一下glance-api.conf和glance-registry.conf配置文件：

```cpp
cp -a /etc/glance/glance-api.conf{,.bak} 
cp -a /etc/glance/glance-registry.conf{,.bak} 
grep -Ev '^$|#' /etc/glance/glance-api.conf.bak > /etc/glance/glance-api.conf 
grep -Ev '^$|#' /etc/glance/glance-registry.conf.bak > /etc/glance/glance-registry.conf
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;配置/etc/glance/glance-api.conf V2版本，其中的各种配置通过openstack-config修改：

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改glance数据库的连接地址：

```cpp
openstack-config --set /etc/glance/glance-api.conf database connection mysql+pymysql://glance:GLANCE_DBPASS@192.168.112.146/glance 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改keystone认证的url：

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken www_authenticate_uri http://192.168.112.146:5000 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改auth_url:

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken auth_url http://192.168.112.146:5000 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改memcached服务器的地址

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken memcached_servers 192.168.112.146:11211 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改glance的认证方式为password

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken auth_type password 
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;修改项目的domain名称为Default

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken project_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改用户的domain名称为Defaultw

```csharp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken user_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改项目名称为service

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken project_name service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改用户名为glance

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken username glance 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改password为GLANCE_PASS

```cpp
openstack-config --set /etc/glance/glance-api.conf keystone_authtoken password GLANCE_PASS 
```

修改

```cpp
openstack-config --set /etc/glance/glance-api.conf paste_deploy flavor keystone 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改glance的存储方式为file或http

```cpp
openstack-config --set /etc/glance/glance-api.conf glance_store stores file,http 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改glance的默认存储方式为file

```cpp
openstack-config --set /etc/glance/glance-api.conf glance_store default_store file 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改glance的文件存储目录

```cpp
openstack-config --set /etc/glance/glance-api.conf glance_store filesystem_store_datadir /var/lib/glance/images/
```


接下来配置**glance-registry.conf，v1版本**:

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改glance数据库的连接地址：

```cpp
openstack-config --set /etc/glance/glance-registry.conf database connection mysql+pymysql://glance:GLANCE_DBPASS@192.168.112.146/glance 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改keystone认证的url：

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken www_authenticate_uri http://192.168.112.146:5000 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改auth_url:

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken auth_url http://192.168.112.146:5000 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改memcached服务器的地址

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken memcached_servers 192.168.112.146:11211 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改glance的认证方式为password

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken auth_type password 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改项目的domain名称为Default

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken project_domain_name Default
```

 

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改用户的domain名称为Defaultw

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken user_domain_name Default 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改项目名称为service

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken project_name service 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改用户名为glance

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken username glance 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改password为GLANCE_PASS

```cpp
openstack-config --set /etc/glance/glance-registry.conf keystone_authtoken password GLANCE_PASS 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改

```cpp
openstack-config --set /etc/glance/glance-registry.conf paste_deploy flavor keystone 
```

**下面的三项并没有设置**：
修改glance的存储方式为file或http

```cpp
openstack-config --set /etc/glance/glance-api.conf glance_store stores file,http 
```

修改glance的默认存储方式为file

```cpp
openstack-config --set /etc/glance/glance-api.conf glance_store default_store file 
```

修改glance的文件存储目录

```cpp
openstack-config --set /etc/glance/glance-api.conf glance_store filesystem_store_datadir /var/lib/glance/images/
```

### 7. 初始化glance数据库

```cpp
su -s /bin/sh -c "glance-manage db_sync" glance
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;初试完glance数据库后，进入mysql中查询glance数据库，存在数据表即可：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062217031933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

### 8. 启动glance服务并设置开机自启 

```cpp
[root@controller ~]# systemctl enable openstack-glance-api.service 
[root@controller ~]# systemctl start openstack-glance-api.service
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;检查服务启动情况，如果有9292端口，则表示glance服务启动成功。
&nbsp;  &nbsp;  &nbsp;  &nbsp;netstat -tunlp 用于显示 tcp，udp 的端口和进程等相关情况。
netstat 查看端口占用语法格式：

```cpp
netstat -tunlp | grep 端口号
•	-t (tcp) 仅显示tcp相关选项
•	-u (udp)仅显示udp相关选项
•	-n 拒绝显示别名，能显示数字的全部转化为数字
•	-l 仅列出在Listen(监听)的服务状态
•	-p 显示建立相关链接的程序名
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062217033199.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以看到，9292端口已经启动占用了。
### 9.创建Image验证glance安装情况
&nbsp;  &nbsp;  &nbsp;  &nbsp;首先获得admin权限 

```cpp
source admin-openrc
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这时候执行如下命令可以看到为空

```cpp
openstack image list 
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170341764.png)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp;因为里面还没有镜像 所以我们要先上传一个

&nbsp;  &nbsp;  &nbsp;  &nbsp;下载一个极小的cirrors镜像：

```cpp
wget http://download.cirros-cloud.net/0.4.0/cirros-0.4.0-x86_64-disk.img
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个镜像cirrors，镜像格式是qcow2，容器的格式是bare，范围是public：

```cpp
openstack image create --file /root/cirros-0.4.0-x86_64-disk.img --disk-format qcow2 --container-format bare --public cirros
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170353681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp;上面是cirrors镜像的所有信息，非常详细 。
（官方文档坑点之一，这里不要使用官方文档里面的glance image-create这样的写法，新版本的OpenStack已经不支持，尽量统一使用以openstack开头的命令写法）

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看当前所有的image信息：

```cpp
openstack image list
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062217040592.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;查看Image对应的物理文件，在默认的images存储目录/var/lib/glance/images下：

```cpp
ll /var/lib/glance/images
```

 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622170417730.png)









