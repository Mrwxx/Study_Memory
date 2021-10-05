@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">



## 1.	server相关命令测试

### （1）查看server相关命令

```cpp
[root@controller neutron]# openstack server -h
Command "server" matches:
  server add fixed ip
  server add floating ip
  server add network
  server add port
  server add security group
  server add volume
  server backup create
  server create
  server delete
  server dump create
  server event list
  server event show
  server group create
  server group delete
  server group list
  server group show
  server image create
  server list
  server lock
  server migrate
  server pause
  server reboot
  server rebuild
  server remove fixed ip
  server remove floating ip
  server remove network
  server remove port
  server remove security group
  server remove volume
  server rescue
  server resize
  server resize confirm
  server resize revert
  server restore
  server resume
  server set
  server shelve
  server show
  server ssh
  server start
  server stop
  server suspend
  server unlock
  server unpause
  server unrescue
  server unset
  server unshelve
```

<br>



### （2）server add fixed ip

&nbsp;  &nbsp;  &nbsp;  &nbsp;为server实例添加固定的IP，参数是network的ID：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/7b7fa28387bc49d1a21ee5dcd925c890.png)

<br>



### （3）server add floating ip

&nbsp;  &nbsp;  &nbsp;  &nbsp;为server实例添加浮动的IP地址
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/d54caad670a241bea893f66931e06aec.png)

<br>



### （4）server add network

&nbsp;  &nbsp;  &nbsp;  &nbsp;为server实例添加network网络：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/8d0a2f868c9e4cc1bff31d0ec3182a79.png)

<br>




### （5）server add port

&nbsp;  &nbsp;  &nbsp;  &nbsp;为server实例添加port端口
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/b9029cb15da44209833f501caf5a8918.png)

<br>



### （6）server add security group

&nbsp;  &nbsp;  &nbsp;  &nbsp;为server实例添加安全组：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/dde6a8a9410c45a78cfed3887e0120b1.png)


<br>




### （7）server add volume

&nbsp;  &nbsp;  &nbsp;  &nbsp;为server实例添加volume卷：
![在这里插入图片描述](https://img-blog.csdnimg.cn/3016029fc3644e0e95f0b790fbf7871c.png)

 <br>



### （8）server backup create

&nbsp;  &nbsp;  &nbsp;  &nbsp;Create a server backup image，创建实例的备份镜像，实际就是通过server实例创建一个镜像。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/b0e07c770ac14b12af3993265ef21560.png)

<br>



### （9）server create
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个server实例：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/c9cd10ef70db473db8ed46c2e9f21ecf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

<br>



### （10）server delete

&nbsp;  &nbsp;  &nbsp;  &nbsp;删除一个server实例：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/62e7193d46ac47dc9349b5e37855b5b1.png)

<br>




### （11）  server dump create

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个dump文件：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/b3fe28c33f644a448a67979ffcd933b8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

<br>



### （12）server event list

&nbsp;  &nbsp;  &nbsp;  &nbsp;列出该server最近的event事件，即实例有哪些操作？
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/b55988412da648399a94aed953790287.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0db4151bb52548ed8a6bc026af4c146a.png)

 <br>



### （13）server event show

&nbsp;  &nbsp;  &nbsp;  &nbsp;列出指定server实例的指定request请求的细节操作：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/02779f2a9db245daaa1181126056213d.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们通过查询server的event事件后，获取到对应server的指定request请求ID后，查询该请求对应的详细信息：

&nbsp;  &nbsp;  &nbsp;  &nbsp;比如说针对firstVM这个实例的start操作，我们可以查询一下该操作的详细信息：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/fbbe7a92fe1542faab1a3bfed53b440c.png)

<br>



### （14）server group create

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个server实例的群组，即多个server实例都放在一个群组中：
![在这里插入图片描述](https://img-blog.csdnimg.cn/64c07cbfeff840169fd9f01132cd44d5.png)

 <br>



### （15）server group delete

&nbsp;  &nbsp;  &nbsp;  &nbsp;删除一个server实例的群组：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/be3af576e9354a9ca609d8d01cf1f132.png)

<br>



### （16）server group list

&nbsp;  &nbsp;  &nbsp;  &nbsp;列出所有的server实例的群组：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/fea16bbbbc5b4e4b8633df15c7c81c36.png)

<br>



### （17）server group show

&nbsp;  &nbsp;  &nbsp;  &nbsp;展示出server实例群组的细节：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/f116f5189fb945ecbbfe3f5c05fd1da5.png)


<br>



### （18）server image create

&nbsp;  &nbsp;  &nbsp;  &nbsp;通过一个server实例创建一个磁盘镜像：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/467fee39e48a490ab673d623b2bef5f9.png)

<br>


### （19）server list

&nbsp;  &nbsp;  &nbsp;  &nbsp;列出所有的server实例：
![在这里插入图片描述](https://img-blog.csdnimg.cn/5ccadde8467547b78e979efd1aac496f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

 <br>



### （20）server lock

&nbsp;  &nbsp;  &nbsp;  &nbsp;将server实例给锁住，让非admin用户不会执行操作：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/5a315b934f8f4a869f9b2b1dda6a8b77.png)

 <br>



### （21）server migrate

&nbsp;  &nbsp;  &nbsp;  &nbsp;迁移一个server实例到不同的计算节点中，也就是说选择另一个计算节点进行迁移：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/27c579650f9249a0953f8bf57bb2fab3.png)

主要参数的解释如下所示：

--live-migration，动态迁移
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/8d06807552ab4426abbc84672e17183a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

一开始通过如下的命令：

```cpp
openstack server migrate --live-migration sixthVM
```

查看nova-api.log日志发现：

```cpp
2021-09-24 14:16:42.586 2289 INFO nova.api.openstack.wsgi [req-de76dedd-c998-4325-9546-0034cfab78e3 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 实例 sixthVM 没有找到。
2021-09-24 14:16:42.589 2289 INFO nova.osapi_compute.wsgi.server [req-de76dedd-c998-4325-9546-0034cfab78e3 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/sixthVM HTTP/1.1" status: 404 len: 517 time: 0.2916870
2021-09-24 14:16:42.730 2289 INFO nova.osapi_compute.wsgi.server [req-69b8762b-d341-42ae-a19c-8709e2dbb3c8 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers?name=sixthVM HTTP/1.1" status: 200 len: 701 time: 0.1353440
2021-09-24 14:16:43.186 2289 INFO nova.osapi_compute.wsgi.server [req-7e77e0ef-7c2b-4b1b-a17e-475ad37a9ec9 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485 HTTP/1.1" status: 200 len: 1959 time: 0.4523001
2021-09-24 14:16:46.180 2289 INFO nova.api.openstack.wsgi [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 找不到有效主机，原因是 没有足够的主机可用。
2021-09-24 14:16:46.181 2289 INFO nova.osapi_compute.wsgi.server [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "POST /v2.1/servers/ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485/action HTTP/1.1" status: 400 len: 600 time: 2.9914739
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;发现到出现了error，找不到有效的主机，这是为什么呢？

继续查看nova-conductor.log日志：

```cpp
2021-09-24 14:16:46.096 2403 WARNING nova.scheduler.utils [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Failed to compute_task_migrate_server: 找不到有效主机，原因是 没有足够的主机可用。。
Traceback (most recent call last):

  File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/server.py", line 235, in inner
    return func(*args, **kwargs)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/manager.py", line 214, in select_destinations
    allocation_request_version, return_alternates)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 96, in select_destinations
    allocation_request_version, return_alternates)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 265, in _schedule
    claimed_instance_uuids)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 302, in _ensure_sufficient_hosts
    raise exception.NoValidHost(reason=reason)

NoValidHost: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
2021-09-24 14:16:46.098 2403 WARNING nova.scheduler.utils [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] Setting instance to ACTIVE state.: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
```

继续查看nova-scheduler.log日志：

可以看到，Host filter过滤器直接忽视了computer这个host；

```cpp
2021-09-24 14:16:45.100 2690 INFO nova.scheduler.host_manager [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Host filter ignoring hosts: computer
2021-09-24 14:16:45.102 2690 WARNING nova.scheduler.filters.compute_filter [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] (localhost.localdomain, localhost.localdomain) ram: 3258MB disk: 32768MB io_ops: 0 instances: 0 has not been heard from in a while
2021-09-24 14:16:45.102 2690 INFO nova.filters [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filter ComputeFilter returned 0 hosts
2021-09-24 14:16:45.102 2690 INFO nova.filters [req-ba341f5a-8855-4a67-8d69-585ae4dc2184 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filtering removed all hosts for the request with instance ID 'ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485'. Filter results: ['AvailabilityZoneFilter: (start: 1, end: 1)', 'ComputeFilter: (start: 1, end: 0)']
```

重新尝试命令，进行冷迁移：

```cpp
openstack server migrate sixthVM
```

查看nova-api.log日志：

```cpp
2021-09-24 15:23:30.305 2289 INFO nova.api.openstack.wsgi [req-5a10aa8f-e1cf-46db-8a10-a0626fe33d04 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 实例 sixthVM 没有找到。
2021-09-24 15:23:30.307 2289 INFO nova.osapi_compute.wsgi.server [req-5a10aa8f-e1cf-46db-8a10-a0626fe33d04 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/sixthVM HTTP/1.1" status: 404 len: 517 time: 0.2010651
2021-09-24 15:23:30.406 2289 INFO nova.osapi_compute.wsgi.server [req-812f1dce-e613-4b09-8aad-5882881ba4a9 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers?name=sixthVM HTTP/1.1" status: 200 len: 701 time: 0.0959730
2021-09-24 15:23:30.712 2289 INFO nova.osapi_compute.wsgi.server [req-982b31b4-1b66-4731-9499-2d2831d21061 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485 HTTP/1.1" status: 200 len: 1959 time: 0.3029451
2021-09-24 15:23:33.855 2289 INFO nova.api.openstack.wsgi [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 找不到有效主机，原因是 冷迁移过程中发现无效主机。
2021-09-24 15:23:33.856 2289 INFO nova.osapi_compute.wsgi.server [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "POST /v2.1/servers/ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485/action HTTP/1.1" status: 400 len: 612 time: 3.1414089
```

查看nova-scheduler.log日志：
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以发现，问题主要在于filter过滤器直接将computer节点过滤掉了，因为我们在迁移虚机时，使用的过滤器应该第一个就是过滤掉之前已经调度过的computer节点：

```cpp
2021-09-24 15:23:33.148 2692 INFO nova.scheduler.host_manager [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Host filter ignoring hosts: computer
2021-09-24 15:23:33.154 2692 WARNING nova.scheduler.filters.compute_filter [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] (localhost.localdomain, localhost.localdomain) ram: 3258MB disk: 32768MB io_ops: 0 instances: 0 has not been heard from in a while
2021-09-24 15:23:33.155 2692 INFO nova.filters [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filter ComputeFilter returned 0 hosts
2021-09-24 15:23:33.155 2692 INFO nova.filters [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filtering removed all hosts for the request with instance ID 'ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485'. Filter results: ['AvailabilityZoneFilter: (start: 1, end: 1)', 'ComputeFilter: (start: 1, end: 0)']
```


查看nova-conductor.log日志：

```cpp
2021-09-24 15:23:33.790 2401 WARNING nova.scheduler.utils [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Failed to compute_task_migrate_server: 找不到有效主机，原因是 没有足够的主机可用。。
Traceback (most recent call last):

  File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/server.py", line 235, in inner
    return func(*args, **kwargs)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/manager.py", line 214, in select_destinations
    allocation_request_version, return_alternates)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 96, in select_destinations
    allocation_request_version, return_alternates)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 265, in _schedule
    claimed_instance_uuids)

  File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 302, in _ensure_sufficient_hosts
    raise exception.NoValidHost(reason=reason)
#如下的都是16进制的字符，翻译过来就是无host可选：
: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
2021-09-24 15:23:33.792 2401 WARNING nova.scheduler.utils [req-1b899c05-41da-4e75-b808-514d351dbc79 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] Setting instance to ACTIVE state.: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看nova.conf配置文件：/etc/nova/nova.conf，修改nova.conf配置文件，添加如下的driver驱动：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/698e49ad03ea40fab076421b5bcfea6b.png)

```cpp
scheduler_driver=nova.scheduler.filterscheduler.FilterScheduler
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;配置可用的filter，默认所有的filter都可以使用:
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/4c9ee4f367c646949eb5fca7564b64b3.png)

```cpp
scheduler_available_filters = nova.scheduler.filters.all_filters
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;还可以指定真正使用的filter，会按照顺序依次地进行过滤：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/71f73938e189486499e81cb92bc7bcdf.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp;将上面的设置去除掉RetryFilter，该filter的作用是去除掉之前调度过的节点；具体的每个filter的作用可以看看openstack的pdf文档：

```cpp
scheduler_default_filters = AvailabilityZoneFilter, RamFilter, DiskFilter, ComputeFilter
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;重启Controller节点和Compute节点；

&nbsp;  &nbsp;  &nbsp;  &nbsp;重新尝试热迁移虚拟机：

```cpp
openstack server migrate --live-migration sixthVM
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/97cc8a767cd9467aaec6c4a76cb02cb5.png)

 
&nbsp;  &nbsp;  &nbsp;  &nbsp;出现报错，因此删除该server虚机：

&nbsp;  &nbsp;  &nbsp;  &nbsp;重新将新的虚机启动，并且尝试冷迁移：

```cpp
openstack server migrate fifthVM
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;同样是失败：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/40b49c91302344ba81b6df9d3afa8041.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;我们查看nova-api.log日志，可以看到一大堆的ERROR错误，
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/d7fe22ac450b4f9788e0d0656e6aabba.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp;大致的错误是消息超时，因此是neutron网络的原因，设置neutron.conf配置文件：
&nbsp;  &nbsp;  &nbsp;  &nbsp;neutron在同步路由信息时，会从neutron-server获取所有router的信息，这个过程会比较长（130s左右，和网络资源的多少有关系），而 在/etc/neutron/neutron.conf中会有一个配置项“rpc_response_timeout”，它用来配置RPC的超时时间，默认为60s,所以导致超时异常.解决方法为设置rpc_response_timeout=180.

&nbsp;  &nbsp;  &nbsp;  &nbsp;重新启动两个虚机；
&nbsp;  &nbsp;  &nbsp;  &nbsp;重新将新的虚机启动，并且尝试冷迁移：

```cpp
openstack server migrate fifthVM
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;依然失败：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/319c88bd02674dbe8cc8e196988e64dd.png)

<br>




### （22）server pause

&nbsp;  &nbsp;  &nbsp;  &nbsp;将server实例短时间暂停，保存在宿主机的内存中，等待resume操作后从内存中读出server实例的状态，然后继续运行server实例，它的状态是paused。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/87f7242fdf2347edabb0eb2f727eedca.png)

<br>



### （23）server reboot

&nbsp;  &nbsp;  &nbsp;  &nbsp;soft reboot重启操作系统，整个过程server依然处于运行状态；
&nbsp;  &nbsp;  &nbsp;  &nbsp;hard reboot重启server实例，相当于关机后再开机，
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/241c6324b66d4ec0b9b9beac120e13fb.png)

<br>



### （24）server rebuild

&nbsp;  &nbsp;  &nbsp;  &nbsp;重建一个server实例，通过之前备份的snapshot镜像恢复当前的server实例：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/0513cd89896b4b7db8ac5050e7aa0102.png)

<br>



### （25）server remove fixed ip

&nbsp;  &nbsp;  &nbsp;  &nbsp;移除server实例的固定IP地址：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/63016ba3640a40259a8aa57ab4c04feb.png)

<br>



### （26）server remove floating ip

&nbsp;  &nbsp;  &nbsp;  &nbsp;移除server实例的浮动IP地址：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/99e9dcf2629945d187cfda8ac0f3506f.png)

<br>


### （27）server remove network

&nbsp;  &nbsp;  &nbsp;  &nbsp;移除server实例的某个network的所有port：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2978897767c248fd87b28f5d314ac3a6.png)

<br>



### （28）server remove port

&nbsp;  &nbsp;  &nbsp;  &nbsp;移除server实例的指定port：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/f638ebb9e3ac45118452eec3a9cfbd00.png)

<br>



### （29）server remove security group

&nbsp;  &nbsp;  &nbsp;  &nbsp;移除server实例的安全组：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/a7772a0e6b1f44088153988036f02786.png)

<br>



### （30）server remove volume
&nbsp;  &nbsp;  &nbsp;  &nbsp;移除server实例的volume卷：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2542ac1f572545baa234a258471e9ca3.png)

<br>



### （31）server rescue

&nbsp;  &nbsp;  &nbsp;  &nbsp;考虑到操作系统故障，无法启动操作系统时，为了最大程度地挽救数据，我们需要使用一张启动盘将系统引导起来，然后再尝试恢复：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d9a1d276eece4f3fa60cb4752a38bc04.png)

 <br>



### （32）server resize

&nbsp;  &nbsp;  &nbsp;  &nbsp;改变server实例的flavor大小，复制原有的server实例内容到新的server实例中，在resize之前借助nova-scheduler重新为server实例选择一个合适的计算节点，如果选择的节点不是同一个，就需要进行migrate；如果选择的是同一个计算节点，则就是resize操作：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/f3784e269b654e8081d183c87c9f91c1.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp;因此创建一个新的flavor：

```cpp
openstack flavor create --id 4 --ram 128 --disk 1 --vcpus 1 m1.nano.new
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建好之后，进行resize操作：

```cpp
openstack server resize --flavor m1.nano.new fifthVM    
```

          

&nbsp;  &nbsp;  &nbsp;  &nbsp;又是MQ的消息超时ERROR：

nova-api.log日志：

```cpp
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi [req-adb018d8-afb7-4668-869c-179b90ea5dbf 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Unexpected exception in API method: MessagingTimeout: Timed out waiting for a reply to message ID ddf0ff7c5a874baa963a728feaaf4c2a
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi Traceback (most recent call last):
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/api/openstack/wsgi.py", line 671, in wrapped
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     return f(*args, **kwargs)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/api/validation/__init__.py", line 110, in wrapper
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     return func(*args, **kwargs)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/api/openstack/compute/servers.py", line 1045, in _action_resize
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     self._resize(req, id, flavor_ref, **kwargs)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/api/openstack/compute/servers.py", line 964, in _resize
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     self.compute_api.resize(context, instance, flavor_id, **kwargs)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/compute/api.py", line 225, in inner
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     return function(self, context, instance, *args, **kwargs)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/compute/api.py", line 152, in inner
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     return f(self, context, instance, *args, **kw)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/compute/api.py", line 215, in wrapped
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     return function(self, context, instance, *args, **kwargs)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/compute/api.py", line 3796, in resize
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     request_spec=request_spec)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/conductor/api.py", line 96, in resize_instance
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     request_spec=request_spec, host_list=host_list)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/nova/conductor/rpcapi.py", line 340, in migrate_server
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     return cctxt.call(context, 'migrate_server', **kw)
#发送RPC远程请求，设置的两个timeout都是从nova.conf中得到的
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/client.py", line 181, in call
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     transport_options=self.transport_options)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/oslo_messaging/transport.py", line 129, in _send
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     transport_options=transport_options)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 674, in send
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     transport_options=transport_options)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 662, in _send
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     call_monitor_timeout)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 551, in wait
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     message = self.waiters.get(msg_id, timeout=timeout)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 429, in get
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi     'to message ID %s' % msg_id)
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi MessagingTimeout: Timed out waiting for a reply to message ID ddf0ff7c5a874baa963a728feaaf4c2a
2021-09-25 14:19:47.564 2249 ERROR nova.api.openstack.wsgi 
2021-09-25 14:19:47.566 2249 INFO nova.api.openstack.wsgi [req-adb018d8-afb7-4668-869c-179b90ea5dbf 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 发生意外 API 错误。请在 http://bugs.launchpad.net/nova/ 处报告此错误，并且附上 Nova API 日志（如果可能）。
<class 'oslo_messaging.exceptions.MessagingTimeout'>
2021-09-25 14:19:47.567 2249 INFO nova.osapi_compute.wsgi.server [req-adb018d8-afb7-4668-869c-179b90ea5dbf 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "POST /v2.1/servers/a2c57cfb-7dd9-42b6-b1e2-454d07cfbe77/action HTTP/1.1" status: 500 len: 755 time: 61.0104420
2021-09-25 14:19:50.621 2249 INFO oslo_messaging._drivers.amqpdriver [-] No calling threads waiting for msg_id : ddf0ff7c5a874baa963a728feaaf4c2a
```

nova-scheduler.log日志：
无内容；

nova-conductor.log日志：

```cpp
2021-09-25 14:18:47.656 2407 WARNING oslo_config.cfg [req-adb018d8-afb7-4668-869c-179b90ea5dbf 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Deprecated: Option "scheduler_default_filters" from group "DEFAULT" is deprecated. Use option "enabled_filters" from group "filter_scheduler".
2021-09-25 14:19:50.517 2407 WARNING nova.scheduler.utils [req-adb018d8-afb7-4668-869c-179b90ea5dbf 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Failed to compute_task_migrate_server: Timed out waiting for a reply to message ID 187df0b0f95a4e27919321830142d187: MessagingTimeout: Timed out waiting for a reply to message ID 187df0b0f95a4e27919321830142d187
#实例设置为ACTIVE状态，消息超时
2021-09-25 14:19:50.522 2407 WARNING nova.scheduler.utils [req-adb018d8-afb7-4668-869c-179b90ea5dbf 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: a2c57cfb-7dd9-42b6-b1e2-454d07cfbe77] Setting instance to ACTIVE state.: MessagingTimeout: Timed out waiting for a reply to message ID 187df0b0f95a4e27919321830142d187
#等待消息超时
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server [req-adb018d8-afb7-4668-869c-179b90ea5dbf 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Exception during message handling: MessagingTimeout: Timed out waiting for a reply to message ID 187df0b0f95a4e27919321830142d187
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server Traceback (most recent call last):
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/server.py", line 165, in _process_incoming
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     res = self.dispatcher.dispatch(message)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/dispatcher.py", line 274, in dispatch
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return self._do_dispatch(endpoint, method, ctxt, args)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/dispatcher.py", line 194, in _do_dispatch
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     result = func(ctxt, **new_args)  
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/server.py", line 235, in inner
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return func(*args, **kwargs)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 95, in wrapper
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return fn(self, context, *args, **kwargs)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/compute/utils.py", line 1372, in decorated_function
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return function(self, context, *args, **kwargs)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 299, in migrate_server
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     host_list)
#从这一步开始出现exception
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 381, in _cold_migrate
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     updates, ex, request_spec)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_utils/excutils.py", line 220, in __exit__
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     self.force_reraise()
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_utils/excutils.py", line 196, in force_reraise
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     six.reraise(self.type_, self.value, self.tb)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 350, in _cold_migrate
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     task.execute()
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/tasks/base.py", line 27, in wrap
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     self.rollback()
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_utils/excutils.py", line 220, in __exit__
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     self.force_reraise()
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_utils/excutils.py", line 196, in force_reraise
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     six.reraise(self.type_, self.value, self.tb)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/tasks/base.py", line 24, in wrap
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return original(self)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/tasks/base.py", line 42, in execute
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return self._execute()
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/tasks/migrate.py", line 340, in _execute
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     selection = self._schedule()
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/conductor/tasks/migrate.py", line 376, in _schedule
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return_objects=True, return_alternates=True)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/scheduler/client/query.py", line 42, in select_destinations
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     instance_uuids, return_objects, return_alternates)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/nova/scheduler/rpcapi.py", line 160, in select_destinations
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     return cctxt.call(ctxt, 'select_destinations', **msg_args)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/client.py", line 181, in call
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     transport_options=self.transport_options)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/transport.py", line 129, in _send
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     transport_options=transport_options)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 674, in send
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     transport_options=transport_options)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 662, in _send
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     call_monitor_timeout)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 551, in wait
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     message = self.waiters.get(msg_id, timeout=timeout)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 429, in get
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server     'to message ID %s' % msg_id)
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server MessagingTimeout: Timed out waiting for a reply to message ID 187df0b0f95a4e27919321830142d187
2021-09-25 14:19:50.616 2407 ERROR oslo_messaging.rpc.server
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;查看到在调用rpc时，有这样的一行代码，其中的timeout参数采用的是nova.conf中的参数，因此我们尝试增大其中的两个timeout参数：

```csharp
 cctxt = self.client.prepare(
            version=version, call_monitor_timeout=CONF.rpc_response_timeout,
             timeout=CONF.long_rpc_timeout)
```

因此在nova.conf配置中添加了两个参数：

```cpp
rpc_response_timeout=300
long_rpc_timeout=300
```

重启Controller节点；

重新尝试resize操作：

```cpp
openstack server resize --flavor m1.nano.new fourthVM
```

时间好像挺长的，这是为啥呢？查看nova-api.log日志中，该日志的内容一直在重复，感觉像是哪里出了问题。

查看Compute节点的nova-compute.log日志，可以看到内容也是一直在重复：

```cpp
2021-09-25 18:39:40.963 2129 INFO nova.compute.manager [-] [instance: 91cf5aa9-4605-4c99-bf0d-f603c6c988df] During sync_power_state the instance has a pending task (resize_prep). Skip.
2021-09-25 18:40:42.936 2129 INFO nova.compute.resource_tracker [req-52189c54-1223-47ae-a960-6b76acd33a45 - - - - -] [instance: 91cf5aa9-4605-4c99-bf0d-f603c6c988df] Updating resource usage from migration 348fdf7d-00b5-46fa-ad7b-704941ebb736
2021-09-25 18:40:42.982 2129 INFO nova.compute.resource_tracker [req-52189c54-1223-47ae-a960-6b76acd33a45 - - - - -] Instance ee0eb8cd-560e-47b6-9c32-fec87a55ffb4 has allocations against this compute host but is not found in the database.
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;看起来跟数据库好像有关系，最后一个日志中的意思是该实例在compute计算节点中是有分配的，但是在数据库中并没有发现，这可能是数据库的信息没有及时地更新。

错误：主机compute没有映射到任何单元
&nbsp;  &nbsp;  &nbsp;  &nbsp;compute节点日志： `Instance xxx has allocations against this compute host but is not found in the database.`
&nbsp;  &nbsp;  &nbsp;  &nbsp;解决：添加计算节点到cell数据库：

```cpp
su -s /bin/sh -c “nova-manage cell_v2 discover_hosts --verbose” nova
```


&nbsp;  &nbsp;  &nbsp;  &nbsp;继续尝试在dashboard中进行resize操作，依然是无尽的重复，等待，虚机的状态一直保持在resize_prep状态，但是nova-api.log日志和nova-compute.log日志中的内容一直在重复，我们如果要停止该虚机的状态，则直接修改数据库即可，如下所示：

先查询数据库状态

```cpp
use nova;
select task_state,vm_state,power_state,display_name,deleted from instances where display_name="fourthVM";
修改 update instances set task_state = NULL, vm_state = 'stopped', power_state = 4 where display_name="fourthVM" and deleted=0;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;在此之后，fourthVM虚机的状态就会修改为shutdown。


&nbsp;  &nbsp;  &nbsp;  &nbsp;再次在nova.conf配置中添加了两个参数：

```cpp
rpc_response_timeout=300
long_rpc_timeout=300
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;并且将所有的scheduler配置全部注释掉，重启Controller节点和Compute节点：

&nbsp;  &nbsp;  &nbsp;  &nbsp;再次尝试resize虚机：

```cpp
openstack server resize --flavor m1.nano.new fourthVM
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;还是失败，因此决定下次再尝试resize和migrate操作！

<br>


### （33）server resize confirm

&nbsp;  &nbsp;  &nbsp;  &nbsp;改变server实例大小，确认flavor大小修改完成同时释放旧的server：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/e029bdfa5c464a27b104fe92840e6535.png)

<br>


### （34）server resize revert

&nbsp;  &nbsp;  &nbsp;  &nbsp;对server实例进行resize，反转server的新旧大小，即释放新的server，重启旧的server。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/759d2412e4bd4570aeb5245b93c97b00.png)


<br>


### （35）server restore

&nbsp;  &nbsp;  &nbsp;  &nbsp;修复某个server：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/bdea4723f7904b0bbaa3f0f388171558.png)

<br>


### （36）server resume

&nbsp;  &nbsp;  &nbsp;  &nbsp;中断后重新开始server实例，可以在pause或者suspend后恢复：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/e37f04a021624e8085d62635ce5d1615.png)

<br>


### （37）server set

&nbsp;  &nbsp;  &nbsp;  &nbsp;设置server实例的属性，
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/3bf85e0ac583410c945dcd9f23c5cae8.png)

<br>


### （38）server shelve

&nbsp;  &nbsp;  &nbsp;  &nbsp;之前通过suspend操作后server实例处于shutdown状态，但是Hypervisor依然在宿主机上为其预留了资源，如果需要释放这些资源，则可以使用shelve来操作，该操作会将server实例作为image保存到glance中，然后在宿主机中删除该server实例：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/070c1a44e6ed4ca68253adff4232eb77.png)


<br>


### （39）server show

&nbsp;  &nbsp;  &nbsp;  &nbsp;展示server实例的详细信息：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/4661a17adc064351b63ecf13bf37f12a.png)

<br>


### （40）server ssh

&nbsp;  &nbsp;  &nbsp;  &nbsp;通过ssh的方式连接到server实例中，可以选择IPV4或是IPV6：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/e1b5e1e24f5c4e82bc386a183f53a6cd.png)

<br>


### （41）server start

&nbsp;  &nbsp;  &nbsp;  &nbsp;启动serve实例：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/b29c559961154eb9b73b24d65a84d7fb.png)

<br>


### （42）server stop

&nbsp;  &nbsp;  &nbsp;  &nbsp;停止server实例：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/1971234b4aae46c6ac0a72ab2b054616.png)

<br>


### （43）server suspend

&nbsp;  &nbsp;  &nbsp;  &nbsp;长时间暂停server实例，将server实例的状态保存在磁盘中，它的状态是shutdown ：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/5e12b38aa2544851a973293b8a42b32c.png)

<br>


### （44）server unlock

&nbsp;  &nbsp;  &nbsp;  &nbsp;解锁server实例：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/283fcd21be6546688a326533e05bd4c7.png)

<br>


### （45）server unpause

&nbsp;  &nbsp;  &nbsp;  &nbsp;恢复暂停的serve实例：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/16bc1e980a3a434faaa11c67d4ea49e9.png)


<br>


### （46）server unrescue

&nbsp;  &nbsp;  &nbsp;  &nbsp;让server实例从救援模式恢复：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/06617e249bd04b11ac30ac5d20446431.png)

<br>


### （47）server unset

&nbsp;  &nbsp;  &nbsp;  &nbsp;不设置server实例的相关属性：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/35ea8d7c9f8a450cbba8d5696262bf26.png)

<br>


### （48）server unshelve

&nbsp;  &nbsp;  &nbsp;  &nbsp;恢复被shelve掉的server实例，因为之前的shevle操作保存了之前server实例的镜像文件，因此unshelve操作其实就是通过该镜像重启一个新的server实例：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/d0e3c2d943cc4780897964473473038b.png)










