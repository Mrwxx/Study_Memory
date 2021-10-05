@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">




## 1.制作qcow2格式的镜像

&nbsp;  &nbsp;  &nbsp;  &nbsp; 之前通过制作qcow2格式的Centos7镜像，上传到glance中成功并且成功创建了单个实例，但是创建第二个实例时失败，怀疑是Compute节点的资源情况不够。因此采用cirrors这个比较小的镜像做实验。qcow2格式的镜像是已经安装好了操作系统的镜像，可以直接快速启动。
<br>



## 2.img格式镜像创建实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的cirros的img格式镜像：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/6450966ecdc14befb3582296dd6bc56e.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 磁盘大小为12M，虚拟大小为44M。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前openstack中上传的镜像：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/cf723b0010bb409e85f3a241a33bb8d9.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前openstakc中的实例类型：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6c5c5e21a3b47b9a723faa417daea43.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过cirrors镜像尝试创建一个新的实例：

```cpp
openstack server create --image cirros --flavor m1.nano secondVM
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/6f79a22cdc7f4a29a0376e63195f9a0a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)



&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看刚刚创建的实例的状态：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/affbd024f6bf4ea1a586e1409be7be78.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建成功啦！！！！！！！通过同一个cirros镜像创建成功了两个实例，我们现在尝试通过分配的IP访问该实例：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过cirros的默认账号和密码登录进去：

```cpp
cirros
gocubsgo
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 然后在/home/cirros目录下创建一个文件，以此来区分两个不同cirros实例；
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/4f9b1218213d4fd5a195dd1eadab7fe9.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 同时启动另一个cirros实例firstVM，目的是为了查看我们在secondVM这个实例中所做的修改是否对firstVM这个实例有影响：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看/home/cirros目录下的情况，发现啥也没有，说明secondVM这个实例的修改对于firstVM这个实例没有影响：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/eb299702006f4a438c388d6c0fea27c6.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的server实例对应的image目录，通过glance的配置文件（/etc/glance/glance-api.conf)查看镜像文件的存储目录：

```cpp
[root@controller var]# cat /etc/glance/glance-api.conf
[DEFAULT]
[cinder]
[cors]
[database]
connection = mysql+pymysql://glance:GLANCE_DBPASS@192.168.112.146/glance
[file]
[glance.store.http.store]
[glance.store.rbd.store]
[glance.store.sheepdog.store]
[glance.store.swift.store]
[glance.store.vmware_datastore.store]
[glance_store]
stores = file,http
default_store = file
filesystem_store_datadir = /var/lib/glance/images/
[image_format]
[keystone_authtoken]
www_authenticate_uri = http://192.168.112.146:5000
auth_url = http://192.168.112.146:5000
memcached_servers = 192.168.112.146:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = GLANCE_PASS
[oslo_concurrency]
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
[oslo_middleware]
[oslo_policy]
[paste_deploy]
flavor = keystone
[profiler]
[store_type_location_strategy]
[task]
[taskflow_executor]
```


&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置的镜像存储目录为：`filesystem_store_datadir = /var/lib/glance/images/`
![在这里插入图片描述](https://img-blog.csdnimg.cn/d71daa091b06446c8c3bd484de5495a9.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/64f88fdf8f544f1f9f4dfa47e01c2ce7.png)

 
 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，第一行对应的镜像是196k，是昨天根据centos7_x86_64镜像创建的一个新的镜像，通过copy on write技术创建的，依赖于原有的镜像，因此它的大小非常的小；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 第二行的镜像大小是1.6G，对应的就是原有的centos7_x86_64镜像；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 第三行的镜像大小是13M，对应的就是原有的cirros镜像；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 但是，并没有发现有新的cirros镜像，说明secondVM实例并没有使用新的cirros镜像，只是在原有的cirros镜像的基础上写的数据。
<br>



## 3.img格式同时创建多个实例

```cpp
openstack server create --image cirros --flavor m1.nano --max 2 thridVM
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/1ec50d3957894e77ae6e695fabfbd187.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的server实例创建情况：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/1eb95a2e4ff5452f93b0aebd9ef525c7.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，我们刚才创建的两个thridVM被设定为了thridVM-1和thridVM-2。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 随意连接一个thridVM-1，可以看到/home/cirros目录中啥也没有，说明使用的就是原有的cirros镜像。

<br>



## 4.通过server实例创建新的镜像

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过firstVM实例创建新的镜像：

```cpp
openstack server image create secondVM
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的image镜像：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/eb629daf7167457683a46a87dfabe046.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_18,color_FFFFFF,t_70,g_se,x_16)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，增多了一个image镜像secondVM，查看secondVM镜像的信息，虚拟大小为1G，磁盘大小为38M：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过新的secondVM镜像创建新的实例

```cpp
openstack server create --image secondVM --flavor m1.nano fifthVM
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/866c6c521885458b98abe5718eed35a6.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过控制台连接刚刚创建的fifthVM
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/7b333ca3733d41c38f1b7a093df4dbf4.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，通过secondVM镜像创建的实例中存在着之前我们创建的文件，说明使用的就是之前的secondVM的镜像。

<br>



## 5.通过日志分析一个server实例的创建过程

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过新的secondVM镜像创建新的实例

```cpp
openstack server create --image secondVM --flavor m1.nano sixthVM
```

### （1）nova-api.log日志

```cpp
#查找云主机类型
2021-09-19 11:50:55.437 2262 INFO nova.api.openstack.wsgi [req-372f0624-5ff4-474b-9cd6-99cec1adf712 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 云主机类型 m1.nano 没有找到。
2021-09-19 11:50:55.454 2262 INFO nova.osapi_compute.wsgi.server [req-372f0624-5ff4-474b-9cd6-99cec1adf712 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/m1.nano HTTP/1.1" status: 404 len: 536 time: 0.2891090
2021-09-19 11:50:55.517 2262 INFO nova.osapi_compute.wsgi.server [req-5f9765a4-96c8-4c07-b652-4b9ebbeac671 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors HTTP/1.1" status: 200 len: 965 time: 0.0569861
2021-09-19 11:50:55.543 2262 INFO nova.osapi_compute.wsgi.server [req-0156ad73-aedb-4bd7-a12b-e645d9c2946c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/0 HTTP/1.1" status: 200 len: 762 time: 0.0169179

#post上传实例
2021-09-19 11:50:56.944 2262 INFO nova.osapi_compute.wsgi.server [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "POST /v2.1/servers HTTP/1.1" status: 202 len: 876 time: 1.3900719
2021-09-19 11:50:57.021 2262 INFO nova.osapi_compute.wsgi.server [req-399dc3ec-0c40-486a-8a45-fa20302847f7 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485 HTTP/1.1" status: 200 len: 1687 time: 0.0738139
2021-09-19 11:50:57.065 2262 INFO nova.osapi_compute.wsgi.server [req-dc65c6a4-78da-4d5c-b31e-c07dcd3d540c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/0 HTTP/1.1" status: 200 len: 762 time: 0.0199430

#为实例分配网络
2021-09-19 11:51:01.979 2261 INFO nova.api.openstack.compute.server_external_events [req-7eae9da5-5672-458a-803e-86402bdbdc5e 038fb453b1f84b81a67da5889666b6d1 6f0e8f4df35d4d27941e31d9125ae94e - default default] Creating event network-changed:ad89cab0-9650-4515-a1e1-92718610733a for instance ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485 on computer
2021-09-19 11:51:01.985 2261 INFO nova.osapi_compute.wsgi.server [req-7eae9da5-5672-458a-803e-86402bdbdc5e 038fb453b1f84b81a67da5889666b6d1 6f0e8f4df35d4d27941e31d9125ae94e - default default] 192.168.112.146 "POST /v2.1/os-server-external-events HTTP/1.1" status: 200 len: 578 time: 0.2803221
2021-09-19 11:51:04.446 2262 INFO nova.api.openstack.compute.server_external_events [req-7721b38d-ee76-464c-8ba7-87ba81499a5c 038fb453b1f84b81a67da5889666b6d1 6f0e8f4df35d4d27941e31d9125ae94e - default default] Creating event network-vif-plugged:ad89cab0-9650-4515-a1e1-92718610733a for instance ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485 on computer
2021-09-19 11:51:04.466 2262 INFO nova.osapi_compute.wsgi.server [req-7721b38d-ee76-464c-8ba7-87ba81499a5c 038fb453b1f84b81a67da5889666b6d1 6f0e8f4df35d4d27941e31d9125ae94e - default default] 192.168.112.146 "POST /v2.1/os-server-external-events HTTP/1.1" status: 200 len: 582 time: 0.4733829
```
<br>



### （2）nova-scheduler.log日志


```cpp
2021-09-19 11:50:57.494 2416 WARNING nova.scheduler.filters.compute_filter [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] (localhost.localdomain, localhost.localdomain) ram: 3258MB disk: 32768MB io_ops: 0 instances: 0 has not been heard from in a while
```
<br>



### （3）nova-conductor.log日志

无
<br>



### （4）nova-compute.log日志
Compute节点的日志：

```cpp
#声明实例成功
2021-09-19 11:50:59.233 1672 INFO nova.compute.claims [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] Claim successful on node computer
#创建镜像
2021-09-19 11:50:59.673 1672 INFO nova.virt.libvirt.driver [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] Creating image
2021-09-19 11:50:59.675 1672 INFO oslo.privsep.daemon [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Running privsep helper: ['sudo', 'nova-rootwrap', '/etc/nova/rootwrap.conf', 'privsep-helper', '--config-file', '/usr/share/nova/nova-dist.conf', '--config-file', '/etc/nova/nova.conf', '--privsep_context', 'nova.privsep.sys_admin_pctxt', '--privsep_sock_path', '/tmp/tmpRZWq5u/privsep.sock']
2021-09-19 11:51:00.594 1672 INFO oslo.privsep.daemon [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Spawned new privsep daemon via rootwrap
2021-09-19 11:51:00.512 3733 INFO oslo.privsep.daemon [-] privsep daemon starting
2021-09-19 11:51:00.518 3733 INFO oslo.privsep.daemon [-] privsep process running with uid/gid: 0/0
2021-09-19 11:51:00.520 3733 INFO oslo.privsep.daemon [-] privsep process running with capabilities (eff/prm/inh): CAP_CHOWN|CAP_DAC_OVERRIDE|CAP_DAC_READ_SEARCH|CAP_FOWNER|CAP_NET_ADMIN|CAP_SYS_ADMIN/CAP_CHOWN|CAP_DAC_OVERRIDE|CAP_DAC_READ_SEARCH|CAP_FOWNER|CAP_NET_ADMIN|CAP_SYS_ADMIN/none
#pid设定为3733
2021-09-19 11:51:00.521 3733 INFO oslo.privsep.daemon [-] privsep daemon running as pid 3733
#创建虚拟网络，连接到虚拟网桥上
2021-09-19 11:51:01.760 1672 INFO os_vif [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Successfully plugged vif VIFBridge(active=False,address=fa:16:3e:1e:6a:71,bridge_name='brq9d470a6f-3b',has_traffic_filtering=True,id=ad89cab0-9650-4515-a1e1-92718610733a,network=Network(9d470a6f-3b69-4105-b0eb-f6ef70a9dec3),plugin='linux_bridge',port_profile=<?>,preserve_on_delete=False,vif_name='tapad89cab0-96')
2021-09-19 11:51:03.099 1672 INFO nova.compute.manager [-] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] VM 已开始 (Lifecycle Event)
2021-09-19 11:51:03.143 1672 INFO nova.compute.manager [req-1402b20e-0df2-495b-a778-2d42fcf4fa35 - - - - -] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] VM 已暂停 (Lifecycle Event)
2021-09-19 11:51:03.231 1672 INFO nova.compute.manager [req-1402b20e-0df2-495b-a778-2d42fcf4fa35 - - - - -] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] During sync_power_state the instance has a pending task (spawning). Skip.
2021-09-19 11:51:04.478 1672 INFO nova.compute.manager [req-1402b20e-0df2-495b-a778-2d42fcf4fa35 - - - - -] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] VM 已恢复 (Lifecycle Event)
#实例成功产出
2021-09-19 11:51:04.485 1672 INFO nova.virt.libvirt.driver [-] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] Instance spawned successfully.
#花了4.81s来产出实例在hypervisor中
2021-09-19 11:51:04.485 1672 INFO nova.compute.manager [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] Took 4.81 seconds to spawn the instance on the hypervisor.
2021-09-19 11:51:04.597 1672 INFO nova.compute.manager [req-1402b20e-0df2-495b-a778-2d42fcf4fa35 - - - - -] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] During sync_power_state the instance has a pending task (spawning). Skip.
#花了6.45s成功创建一个实例
2021-09-19 11:51:04.614 1672 INFO nova.compute.manager [req-bfe681b0-e4e9-433f-bcee-06299256996a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485] Took 6.45 seconds to build instance.
```
<br>



### （5）glance的api.log日志

```cpp
#获取secondVM镜像的信息
2021-09-19 11:50:55.089 2206 INFO eventlet.wsgi.server [req-105a4600-3c92-43cc-869c-70711f069bef 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [19/Sep/2021 11:50:55] "GET /v2/images/secondVM HTTP/1.1" 404 442 0.909730
2021-09-19 11:50:55.125 2206 INFO eventlet.wsgi.server [req-88fa9d83-42d7-4f70-b7dd-dc9598cbbfea 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [19/Sep/2021 11:50:55] "GET /v2/images?limit=20 HTTP/1.1" 200 4984 0.032838
2021-09-19 11:50:55.133 2206 INFO eventlet.wsgi.server [req-1bcbe233-44f5-46ef-909d-9743961a0ba4 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [19/Sep/2021 11:50:55] "GET /v2/schemas/image HTTP/1.1" 200 6925 0.005036
2021-09-19 11:50:56.321 2206 WARNING keystonemiddleware.auth_token [-] A valid token was submitted as a service token, but it was not a valid service token. This is incorrect but backwards compatible behaviour. This will be removed in future releases.
2021-09-19 11:50:56.338 2206 INFO eventlet.wsgi.server [req-8262a5a9-6e0f-4b55-9dba-43f991dbe406 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [19/Sep/2021 11:50:56] "GET /v2/images/f212beae-2ed5-4bef-bb0d-804afcfbb277 HTTP/1.1" 200 1372 0.274619
2021-09-19 11:50:56.346 2206 INFO eventlet.wsgi.server [req-9ba31385-b2d4-4ed4-a5ee-29c83151ab66 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [19/Sep/2021 11:50:56] "GET /v2/schemas/image HTTP/1.1" 200 6925 0.002850
2021-09-19 11:50:57.042 2206 INFO eventlet.wsgi.server [req-c4b39ff5-c0d4-4287-a4e8-1cc2135c26bb 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [19/Sep/2021 11:50:57] "GET /v2/images/f212beae-2ed5-4bef-bb0d-804afcfbb277 HTTP/1.1" 200 1372 0.016544
```
<br>



### （6）keystone的keystone.log日志

```cpp
2021-09-19 12:51:27.181 6500 WARNING keystone.common.rbac_enforcer.enforcer [req-3426a6ab-bed8-4275-bc9d-c3063b91af85 984daa4030fe4b45831bde745c2679a2 6f0e8f4df35d4d27941e31d9125ae94e - default default] Deprecated policy rules found. Use oslopolicy-policy-generator and oslopolicy-policy-upgrade to detect and resolve deprecated policies in your configuration.          
```

                                                                                                                                                                                                                                                                                                                                   	                                                                                                                                                                                                                                                                                                                                             
<br>



### （7）neutron的dhcp-agent.log日志

```cpp
#分配了MAC地址，IP地址
2021-09-19 11:51:00.136 1052 INFO neutron.agent.dhcp.agent [-] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=, binding:profile=, binding:vif_details=, binding:vif_type=unbound, binding:vnic_type=normal, created_at=2021-09-19T03:50:59Z, description=, device_id=ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485, device_owner=, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.209'}], id=ad89cab0-9650-4515-a1e1-92718610733a, mac_address=fa:16:3e:1e:6a:71, name=, network=admin_state_up=True, availability_zone_hints=[], availability_zones=[u'nova'], created_at=2021-09-08T16:39:27Z, description=, id=9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, ipv4_address_scope=None, ipv6_address_scope=None, mtu=1500, name=WAN, port_security_enabled=True, project_id=a7d812868cb74f1a978035530f55f1d0, provider:network_type=flat, provider:physical_network=provider, provider:segmentation_id=None, revision_number=2, router:external=False, shared=True, status=ACTIVE, subnets=[u'6702625f-2d76-4074-8c7c-6a87a6b82525'], tags=[], tenant_id=a7d812868cb74f1a978035530f55f1d0, updated_at=2021-09-08T17:27:57Z, vlan_transparent=None, network_id=9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, port_security_enabled=True, project_id=a7d812868cb74f1a978035530f55f1d0, revision_number=1, security_groups=[u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], status=DOWN, tags=[], tenant_id=a7d812868cb74f1a978035530f55f1d0, updated_at=2021-09-19T03:50:59Z on network _ns_name=qdhcp-9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, admin_state_up=True, availability_zone_hints=[], availability_zones=[u'nova'], created_at=2021-09-08T16:39:27Z, description=, id=9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, ipv4_address_scope=None, ipv6_address_scope=None, mtu=1500, name=WAN, non_local_subnets=[], port_security_enabled=True, ports=[{u'status': u'BUILD', u'binding:host_id': u'controller', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-19T03:32:15Z', u'device_owner': u'network:dhcp', u'revision_number': 15, u'binding:profile': {}, u'port_security_enabled': False, u'fixed_ips': [{'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', 'subnet': {u'description': u'', u'tags': [], u'updated_at': u'2021-09-08T17:27:57Z', u'ipv6_ra_mode': None, u'allocation_pools': [{u'start': u'192.168.112.200', u'end': u'192.168.112.230'}], u'host_routes': [], u'revision_number': 0, u'ipv6_address_mode': None, u'cidr': u'192.168.112.0/24', u'id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'subnetpool_id': None, u'service_types': [], u'name': u'subnet-wan', u'enable_dhcp': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:57Z', u'dns_nameservers': [u'223.5.5.5'], u'gateway_ip': u'192.168.112.254', u'ip_version': 4, u'shared': True, u'project_id': u'a7d812868cb74f1a978035530f55f1d0'}, 'ip_address': u'192.168.112.200'}], u'id': u'3c7aba5e-5ce8-49ea-9ad9-5baaf450facf', u'security_groups': [], u'device_id': u'dhcpd3377d3c-a0d1-5d71-9947-f17125c357bb-9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:90:74:f8', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:58Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T13:43:28Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.225'}], u'id': u'8ecb5d7b-5063-41da-89da-410c6c3dae99', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'9f0ddabf-fb1d-484f-b92d-634f579b4d58', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:b2:11:86', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T10:24:49Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:22:46Z', u'device_owner': u'compute:nova', u'revision_number': 18, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.230'}], u'id': u'9d5a85c3-e486-4393-8e57-0bdc3cbde174', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'11eba17b-4496-4fc2-8fe5-5492b60d9888', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:45:b5:bc', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:43:42Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:17:54Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.226'}], u'id': u'af99ec62-1d5d-4285-97a7-deaee9d8b5a1', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'91cf5aa9-4605-4c99-bf0d-f603c6c988df', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:a1:5a:bf', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T14:12:33Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:24:42Z', u'device_owner': u'compute:nova', u'revision_number': 8, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.217'}], u'id': u'c6ab98e6-0968-4aab-b757-08cef4da50b9', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'4e1b57d1-7f62-4a0c-aa23-c4986036a913', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:ed:be:4c', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T08:48:26Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:35:04Z', u'device_owner': u'compute:nova', u'revision_number': 5, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.224'}], u'id': u'd8339ab9-8e76-481e-a1ce-937b58e365cd', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'a2c57cfb-7dd9-42b6-b1e2-454d07cfbe77', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:ed:08:3c', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T14:34:50Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T13:43:24Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.203'}], u'id': u'f299bfe9-13b6-4855-aae9-9b3682e60154', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'dfaeb9cc-ced5-4310-91f5-51c96a541ec0', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:dc:8f:cb', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T10:24:47Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-17T07:19:13Z', u'device_owner': u'compute:nova', u'revision_number': 7, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.213'}], u'id': u'fc4b20dc-bfc1-4315-a306-28c07359022a', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'b266e572-def0-4067-866e-ee54af85f83f', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:f2:7f:ae', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-13T13:39:11Z'}], project_id=a7d812868cb74f1a978035530f55f1d0, provider:network_type=flat, provider:physical_network=provider, provider:segmentation_id=None, revision_number=2, router:external=False, shared=True, status=ACTIVE, subnets=[{u'description': u'', u'tags': [], u'updated_at': u'2021-09-08T17:27:57Z', u'ipv6_ra_mode': None, u'allocation_pools': [{u'start': u'192.168.112.200', u'end': u'192.168.112.230'}], u'host_routes': [], u'revision_number': 0, u'ipv6_address_mode': None, u'cidr': u'192.168.112.0/24', u'id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'subnetpool_id': None, u'service_types': [], u'name': u'subnet-wan', u'enable_dhcp': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:57Z', u'dns_nameservers': [u'223.5.5.5'], u'gateway_ip': u'192.168.112.254', u'ip_version': 4, u'shared': True, u'project_id': u'a7d812868cb74f1a978035530f55f1d0'}], tags=[], tenant_id=a7d812868cb74f1a978035530f55f1d0, updated_at=2021-09-08T17:27:57Z, vlan_transparent=None

#dhcp配置完成
2021-09-19 11:51:00.590 1052 INFO neutron.agent.dhcp.agent [req-3c66aab1-2bb0-4e03-962d-83386644b0b1 - - - - -] DHCP configuration for ports set([u'ad89cab0-9650-4515-a1e1-92718610733a']) is completed
2021-09-19 11:51:00.701 1052 INFO neutron.agent.dhcp.agent [-] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=computer, binding:profile=, binding:vif_details=, binding:vif_type=unbound, binding:vnic_type=normal, created_at=2021-09-19T03:50:59Z, description=, device_id=ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485, device_owner=compute:nova, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.209'}], id=ad89cab0-9650-4515-a1e1-92718610733a, mac_address=fa:16:3e:1e:6a:71, name=, network_id=9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, port_security_enabled=True, project_id=a7d812868cb74f1a978035530f55f1d0, revision_number=2, security_groups=[u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], status=DOWN, tags=[], tenant_id=a7d812868cb74f1a978035530f55f1d0, updated_at=2021-09-19T03:51:00Z on network _ns_name=qdhcp-9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, admin_state_up=True, availability_zone_hints=[], availability_zones=[u'nova'], created_at=2021-09-08T16:39:27Z, description=, id=9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, ipv4_address_scope=None, ipv6_address_scope=None, mtu=1500, name=WAN, non_local_subnets=[], port_security_enabled=True, ports=[{u'status': u'BUILD', u'binding:host_id': u'controller', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-19T03:32:15Z', u'device_owner': u'network:dhcp', u'revision_number': 15, u'binding:profile': {}, u'port_security_enabled': False, u'fixed_ips': [{'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', 'subnet': {u'description': u'', u'tags': [], u'updated_at': u'2021-09-08T17:27:57Z', u'ipv6_ra_mode': None, u'allocation_pools': [{u'start': u'192.168.112.200', u'end': u'192.168.112.230'}], u'host_routes': [], u'revision_number': 0, u'ipv6_address_mode': None, u'cidr': u'192.168.112.0/24', u'id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'subnetpool_id': None, u'service_types': [], u'name': u'subnet-wan', u'enable_dhcp': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:57Z', u'dns_nameservers': [u'223.5.5.5'], u'gateway_ip': u'192.168.112.254', u'ip_version': 4, u'shared': True, u'project_id': u'a7d812868cb74f1a978035530f55f1d0'}, 'ip_address': u'192.168.112.200'}], u'id': u'3c7aba5e-5ce8-49ea-9ad9-5baaf450facf', u'security_groups': [], u'device_id': u'dhcpd3377d3c-a0d1-5d71-9947-f17125c357bb-9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:90:74:f8', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:58Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T13:43:28Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.225'}], u'id': u'8ecb5d7b-5063-41da-89da-410c6c3dae99', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'9f0ddabf-fb1d-484f-b92d-634f579b4d58', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:b2:11:86', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T10:24:49Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:22:46Z', u'device_owner': u'compute:nova', u'revision_number': 18, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.230'}], u'id': u'9d5a85c3-e486-4393-8e57-0bdc3cbde174', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'11eba17b-4496-4fc2-8fe5-5492b60d9888', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:45:b5:bc', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:43:42Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:17:54Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.226'}], u'id': u'af99ec62-1d5d-4285-97a7-deaee9d8b5a1', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'91cf5aa9-4605-4c99-bf0d-f603c6c988df', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:a1:5a:bf', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T14:12:33Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:24:42Z', u'device_owner': u'compute:nova', u'revision_number': 8, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.217'}], u'id': u'c6ab98e6-0968-4aab-b757-08cef4da50b9', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'4e1b57d1-7f62-4a0c-aa23-c4986036a913', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:ed:be:4c', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T08:48:26Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:35:04Z', u'device_owner': u'compute:nova', u'revision_number': 5, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.224'}], u'id': u'd8339ab9-8e76-481e-a1ce-937b58e365cd', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'a2c57cfb-7dd9-42b6-b1e2-454d07cfbe77', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:ed:08:3c', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T14:34:50Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T13:43:24Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.203'}], u'id': u'f299bfe9-13b6-4855-aae9-9b3682e60154', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'dfaeb9cc-ced5-4310-91f5-51c96a541ec0', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:dc:8f:cb', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T10:24:47Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-17T07:19:13Z', u'device_owner': u'compute:nova', u'revision_number': 7, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.213'}], u'id': u'fc4b20dc-bfc1-4315-a306-28c07359022a', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'b266e572-def0-4067-866e-ee54af85f83f', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:f2:7f:ae', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-13T13:39:11Z'}, {u'status': u'DOWN', u'binding:host_id': u'', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-19T03:50:59Z', u'device_owner': u'', u'revision_number': 1, u'port_security_enabled': True, u'binding:profile': {}, u'binding:vnic_type': u'normal', u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.209'}], u'id': u'ad89cab0-9650-4515-a1e1-92718610733a', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485', u'network': {u'status': u'ACTIVE', u'subnets': [u'6702625f-2d76-4074-8c7c-6a87a6b82525'], u'description': u'', u'provider:physical_network': u'provider', u'tags': [], u'ipv6_address_scope': None, u'updated_at': u'2021-09-08T17:27:57Z', u'revision_number': 2, u'port_security_enabled': True, u'mtu': 1500, u'id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'provider:segmentation_id': None, u'router:external': False, u'availability_zone_hints': [], u'availability_zones': [u'nova'], u'name': u'WAN', u'admin_state_up': True, u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T16:39:27Z', u'provider:network_type': u'flat', u'ipv4_address_scope': None, u'vlan_transparent': None, u'shared': True, u'project_id': u'a7d812868cb74f1a978035530f55f1d0'}, u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {}, u'name': u'', u'binding:vif_type': u'unbound', u'mac_address': u'fa:16:3e:1e:6a:71', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-19T03:50:59Z'}], project_id=a7d812868cb74f1a978035530f55f1d0, provider:network_type=flat, provider:physical_network=provider, provider:segmentation_id=None, revision_number=2, router:external=False, shared=True, status=ACTIVE, subnets=[{u'description': u'', u'tags': [], u'updated_at': u'2021-09-08T17:27:57Z', u'ipv6_ra_mode': None, u'allocation_pools': [{u'start': u'192.168.112.200', u'end': u'192.168.112.230'}], u'host_routes': [], u'revision_number': 0, u'ipv6_address_mode': None, u'cidr': u'192.168.112.0/24', u'id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'subnetpool_id': None, u'service_types': [], u'name': u'subnet-wan', u'enable_dhcp': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:57Z', u'dns_nameservers': [u'223.5.5.5'], u'gateway_ip': u'192.168.112.254', u'ip_version': 4, u'shared': True, u'project_id': u'a7d812868cb74f1a978035530f55f1d0'}], tags=[], tenant_id=a7d812868cb74f1a978035530f55f1d0, updated_at=2021-09-08T17:27:57Z, vlan_transparent=None
2021-09-19 11:51:00.978 1052 INFO neutron.agent.dhcp.agent [-] Trigger reload_allocations for port admin_state_up=True, allowed_address_pairs=[], binding:host_id=computer, binding:profile=, binding:vif_details=connectivity=l2, port_filter=True, binding:vif_type=bridge, binding:vnic_type=normal, created_at=2021-09-19T03:50:59Z, description=, device_id=ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485, device_owner=compute:nova, extra_dhcp_opts=[], fixed_ips=[{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.209'}], id=ad89cab0-9650-4515-a1e1-92718610733a, mac_address=fa:16:3e:1e:6a:71, name=, network_id=9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, port_security_enabled=True, project_id=a7d812868cb74f1a978035530f55f1d0, revision_number=3, security_groups=[u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], status=DOWN, tags=[], tenant_id=a7d812868cb74f1a978035530f55f1d0, updated_at=2021-09-19T03:51:00Z on network _ns_name=qdhcp-9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, admin_state_up=True, availability_zone_hints=[], availability_zones=[u'nova'], created_at=2021-09-08T16:39:27Z, description=, id=9d470a6f-3b69-4105-b0eb-f6ef70a9dec3, ipv4_address_scope=None, ipv6_address_scope=None, mtu=1500, name=WAN, non_local_subnets=[], port_security_enabled=True, ports=[{u'status': u'BUILD', u'binding:host_id': u'controller', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-19T03:32:15Z', u'device_owner': u'network:dhcp', u'revision_number': 15, u'binding:profile': {}, u'port_security_enabled': False, u'fixed_ips': [{'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', 'subnet': {u'description': u'', u'tags': [], u'updated_at': u'2021-09-08T17:27:57Z', u'ipv6_ra_mode': None, u'allocation_pools': [{u'start': u'192.168.112.200', u'end': u'192.168.112.230'}], u'host_routes': [], u'revision_number': 0, u'ipv6_address_mode': None, u'cidr': u'192.168.112.0/24', u'id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'subnetpool_id': None, u'service_types': [], u'name': u'subnet-wan', u'enable_dhcp': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:57Z', u'dns_nameservers': [u'223.5.5.5'], u'gateway_ip': u'192.168.112.254', u'ip_version': 4, u'shared': True, u'project_id': u'a7d812868cb74f1a978035530f55f1d0'}, 'ip_address': u'192.168.112.200'}], u'id': u'3c7aba5e-5ce8-49ea-9ad9-5baaf450facf', u'security_groups': [], u'device_id': u'dhcpd3377d3c-a0d1-5d71-9947-f17125c357bb-9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:90:74:f8', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:58Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T13:43:28Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.225'}], u'id': u'8ecb5d7b-5063-41da-89da-410c6c3dae99', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'9f0ddabf-fb1d-484f-b92d-634f579b4d58', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:b2:11:86', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T10:24:49Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:22:46Z', u'device_owner': u'compute:nova', u'revision_number': 18, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.230'}], u'id': u'9d5a85c3-e486-4393-8e57-0bdc3cbde174', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'11eba17b-4496-4fc2-8fe5-5492b60d9888', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:45:b5:bc', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:43:42Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:17:54Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.226'}], u'id': u'af99ec62-1d5d-4285-97a7-deaee9d8b5a1', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'91cf5aa9-4605-4c99-bf0d-f603c6c988df', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:a1:5a:bf', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T14:12:33Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:24:42Z', u'device_owner': u'compute:nova', u'revision_number': 8, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.217'}], u'id': u'c6ab98e6-0968-4aab-b757-08cef4da50b9', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'4e1b57d1-7f62-4a0c-aa23-c4986036a913', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:ed:be:4c', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T08:48:26Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T14:35:04Z', u'device_owner': u'compute:nova', u'revision_number': 5, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.224'}], u'id': u'd8339ab9-8e76-481e-a1ce-937b58e365cd', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'a2c57cfb-7dd9-42b6-b1e2-454d07cfbe77', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:ed:08:3c', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T14:34:50Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-18T13:43:24Z', u'device_owner': u'compute:nova', u'revision_number': 6, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.203'}], u'id': u'f299bfe9-13b6-4855-aae9-9b3682e60154', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'dfaeb9cc-ced5-4310-91f5-51c96a541ec0', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:dc:8f:cb', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-18T10:24:47Z'}, {u'status': u'ACTIVE', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-17T07:19:13Z', u'device_owner': u'compute:nova', u'revision_number': 7, u'binding:profile': {}, u'port_security_enabled': True, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.213'}], u'id': u'fc4b20dc-bfc1-4315-a306-28c07359022a', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'b266e572-def0-4067-866e-ee54af85f83f', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {u'connectivity': u'l2', u'port_filter': True}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'bridge', u'mac_address': u'fa:16:3e:f2:7f:ae', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-13T13:39:11Z'}, {u'status': u'DOWN', u'binding:host_id': u'computer', u'description': u'', u'allowed_address_pairs': [], u'tags': [], u'extra_dhcp_opts': [], u'updated_at': u'2021-09-19T03:51:00Z', u'device_owner': u'compute:nova', u'revision_number': 2, u'port_security_enabled': True, u'binding:profile': {}, u'fixed_ips': [{u'subnet_id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'ip_address': u'192.168.112.209'}], u'id': u'ad89cab0-9650-4515-a1e1-92718610733a', u'security_groups': [u'f71074ff-7cbe-45d9-abba-e62ead7daae7'], u'device_id': u'ac91ed2c-6c0e-4865-afcf-2b1b9fcb5485', u'name': u'', u'admin_state_up': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'binding:vif_details': {}, u'binding:vnic_type': u'normal', u'binding:vif_type': u'unbound', u'mac_address': u'fa:16:3e:1e:6a:71', u'project_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-19T03:50:59Z'}], project_id=a7d812868cb74f1a978035530f55f1d0, provider:network_type=flat, provider:physical_network=provider, provider:segmentation_id=None, revision_number=2, router:external=False, shared=True, status=ACTIVE, subnets=[{u'description': u'', u'tags': [], u'updated_at': u'2021-09-08T17:27:57Z', u'ipv6_ra_mode': None, u'allocation_pools': [{u'start': u'192.168.112.200', u'end': u'192.168.112.230'}], u'host_routes': [], u'revision_number': 0, u'ipv6_address_mode': None, u'cidr': u'192.168.112.0/24', u'id': u'6702625f-2d76-4074-8c7c-6a87a6b82525', u'subnetpool_id': None, u'service_types': [], u'name': u'subnet-wan', u'enable_dhcp': True, u'network_id': u'9d470a6f-3b69-4105-b0eb-f6ef70a9dec3', u'tenant_id': u'a7d812868cb74f1a978035530f55f1d0', u'created_at': u'2021-09-08T17:27:57Z', u'dns_nameservers': [u'223.5.5.5'], u'gateway_ip': u'192.168.112.254', u'ip_version': 4, u'shared': True, u'project_id': u'a7d812868cb74f1a978035530f55f1d0'}], tags=[], tenant_id=a7d812868cb74f1a978035530f55f1d0, updated_at=2021-09-08T17:27:57Z, vlan_transparent=None
2021-09-19 11:51:01.017 1052 INFO neutron.agent.dhcp.agent [req-591257f7-457c-4850-b0a9-a0ad270d2b94 - - - - -] DHCP configuration for ports set([u'ad89cab0-9650-4515-a1e1-92718610733a']) is completed
2021-09-19 11:51:01.241 1052 INFO neutron.agent.dhcp.agent [req-f8994e62-35b3-4ee8-ae5d-49339fdb2a36 - - - - -] DHCP configuration for ports set([u'ad89cab0-9650-4515-a1e1-92718610733a']) is completed
```
<br>



### （8）neutron的linux-bridge.log日志

```cpp
#安全组更新
2021-09-19 11:51:00.172 1094 INFO neutron.agent.securitygroups_rpc [req-a0223b2e-e629-48e7-8653-ea321abfe4e9 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - - -] Security group member updated [u'f71074ff-7cbe-45d9-abba-e62ead7daae7']
```







