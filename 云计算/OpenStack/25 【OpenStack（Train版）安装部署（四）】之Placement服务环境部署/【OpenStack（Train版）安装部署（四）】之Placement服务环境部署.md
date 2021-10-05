@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．Placement服务安装
### （一）介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp;在安装Nova模块之前，需先安装Placement组件，Placement在openstack的Stein版本之前，属于Nova组件的一部分。该组件需要在Nova之前安装。

&nbsp;  &nbsp;  &nbsp;  &nbsp;Placement服务跟踪资源（比如计算节点，存储资源池，网络资源池等）的使用情况，提供自定义资源的能力，为分配资源提供服务。

&nbsp;  &nbsp;  &nbsp;  &nbsp;Placement在openstack的Stein版本之前，属于Nova组件的一部分。该组件应该在Nova之前安装。

&nbsp;  &nbsp;  &nbsp;  &nbsp;Placement API作为一个标准的WSGI脚本，为WSGI服务器提供了模块级的应用程序属性。
<br>


### （二）placement服务组件
&nbsp;  &nbsp;  &nbsp;  &nbsp;1、Placement提供了placement-api WSGI脚本，用于与Apache，nginx或其他支持WSGI的Web服务器一起运行服务(通过nginx或apache实现python入口代理)。根据用于部署OpenStack的打包解决方案，WSGI脚本可能位于/usr/bin 或/usr/local/bin中

&nbsp;  &nbsp;  &nbsp;  &nbsp;2、Placement服务是从 S 版本，从nova服务中拆分出来的组件，作用是收集各个node节点的可用资源，把node节点的资源统计写入到mysql,Placement服务会被nova scheduler服务进行调用 Placement服务的监听端口是8778

&nbsp;  &nbsp;  &nbsp;  &nbsp;3、需修改的配置文件：

&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）placement.conf

主要修改思路：
&nbsp;  &nbsp;  &nbsp;  &nbsp;Keystone认证相关（url、HOST:PORT、域、账号密码等）

&nbsp;  &nbsp;  &nbsp;  &nbsp;对接数据库（位置）

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2） 00-placement-api.conf
主要修改思路：

&nbsp;  &nbsp;  &nbsp;  &nbsp;Apache权限、访问控制
<br>


### （三）Placement服务部署
#### 1.创建Placement的数据库 

```cpp
[root@controller ~]# mysql -uroot -p 
MariaDB [(none)]> CREATE DATABASE placement; 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;授予Placement数据库本地登录以及远程登录权限

```cpp
MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'localhost' IDENTIFIED BY 'PLACEMENT_DBPASS'; 
MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' IDENTIFIED BY 'PLACEMENT_DBPASS'; 
MariaDB [(none)]> exit； 
```
<br>


#### 2.创建placement用户 

```cpp
[root@controller ~]# openstack user create --domain default --password PLACEMENT_PASS placement
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;添加placement用户为admin角色，service项目

```cpp
[root@controller ~]# openstack role add --project service --user placement admin
```
<br>


#### 3.创建一个placement服务，服务类型为placement

```cpp
[root@controller ~]# openstack service create --name placement --description "Placement API" placement
```
<br>


#### 4、注册Placement服务API访问端口
&nbsp;  &nbsp;  &nbsp;  &nbsp;注册三个API到placement的service中；注册的信息会写入到mysql中

```cpp
[root@controller ~]# openstack endpoint create --region RegionOne placement public http://192.168.112.146:8778

[root@controller ~]# openstack endpoint create --region RegionOne placement internal http://192.168.112.146:8778

[root@controller ~]# openstack endpoint create --region RegionOne placement admin http://192.168.112.146:8778
```
<br>


#### 5. 安装placement软件包 

```cpp
yum install openstack-placement-api -y
```
<br>


#### 6. 修改配置文件 
&nbsp;  &nbsp;  &nbsp;  &nbsp;精简配置文件，删除注释内容

```cpp
[root@controller ~]# cp /etc/placement/placement.conf /etc/placement/placement.conf.bak 
[root@controller ~]# grep -Ev '^$|#' /etc/placement/placement.conf.bak > /etc/placement/placement.conf
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改placement数据库连接地址

```cpp
openstack-config --set /etc/placement/placement.conf placement_database connection mysql+pymysql://placement:PLACEMENT_DBPASS@192.168.112.146/placement
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改placement的认证策略为keystone

```cpp
openstack-config --set /etc/placement/placement.conf api auth_strategy keystone
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改placement的认证地址

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken auth_url  http://192.168.112.146:5000/v3
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改memcached服务器的地址

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken memcached_servers 192.168.112.146:11211
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改认证方式为password

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken auth_type password
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改项目domain名称为Default

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken project_domain_name Default
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改用户的domain名称为Default

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken user_domain_name Default
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改项目名称为service

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken project_name service
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改用户名为placement

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken username placement
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改认证密码为PLACEMENT_PASS

```cpp
openstack-config --set /etc/placement/placement.conf keystone_authtoken password PLACEMENT_PASS
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看当前的placement.conf配置文件：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210623074822481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
<br>


#### 7.初始化placement数据库

```cpp
[root@controller ~]# su -s /bin/sh -c "placement-manage db sync" placement
```

检查数据库初始情况：

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210623074832783.png)
<br>


#### 8.修改Apache配置文件 00-placemenct-api.conf
&nbsp;  &nbsp;  &nbsp;  &nbsp;安装完placement服务后会自动创建该文件-虚拟主机配置	， 修改placement的apache配置文件（官方文档坑点之一，这个步骤官方文档没有提到，如果不做，后面计算服务检查时将会报错）

&nbsp;  &nbsp;  &nbsp;  &nbsp;此处是bug，必须添加下面的配置来启用对placement api的访问，否则在访问apache的api时会报403；添加在文件的最后即可

```cpp
[root@controller ~]# vim /etc/httpd/conf.d/00-placement-api.conf
```

在该文件的末尾添加：

```cpp
<Directory /usr/bin>		
<IfVersion >= 2.4>		   
  Require all granted
</IfVersion>
<IfVersion < 2.4>		    #apache版本；允许apache访问/usr/bin目录；否则/usr/bin/placement-api将不允许被访问
  Order allow,deny				
  Allow from all			#允许apache访问
</IfVersion>
</Directory>
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意是在VirtualHost选项内，如下所示，而不是在末尾：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062307484456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
<br>


#### 9.重启apache服务 

```cpp
[root@controller ~]# systemctl restart httpd
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;httpd服务重启情况正常。

&nbsp;  &nbsp;  &nbsp;  &nbsp;再检查服务是否启动成功，使用netstat -tnlup查看端口情况，如果存在8778的端口，表示placement服务启动成功。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210623074857507.png)
<br>


#### 10.检查placement状态
&nbsp;  &nbsp;  &nbsp;  &nbsp;进一步检查，使用命令：

```cpp
curl http://192.168.112.146:8778
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;直接访问placement的API地址，看是否能返回json。 

```cpp
[root@controller ~]# curl http://controller:8778
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;或者`http://192.168.112.146:8778` 都可以返回json：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210623074911160.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;检查placement状态

```cpp
[root@ct placement]# placement-status upgrade check
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210623074918130.png)
 












