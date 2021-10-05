@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 1.glance中win7虚拟机测试
### （1）-失败-win7的qcow2格式镜像上传glance

&nbsp;  &nbsp;  &nbsp;  &nbsp; 将之前创建好的qcow2格式镜像上传到Controller节点中：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/bee21510584b49a2a39f318ad1c26e8f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 将qcow2格式的镜像上传到glance中：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 上传qcow2格式镜像：

```cpp
glance image-create --name "win7_xshell_qcow2" --file /vhost/win7_xshell.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/65e9ba546dbe4d068c19da8764ce2401.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的glance中的镜像情况：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/acb274d374be4e7ba1f7c041861f1caf.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在默认的glance的image目录下查看image情况：

&nbsp;  &nbsp;  &nbsp;  &nbsp; /var/lib/glance/images目录中的image镜像已经被上传了：

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/006bdb4bc95648c5811175796624acb5.png)
<br>




### （2）-失败-win7虚拟机创建测试（dashboard图形化）

&nbsp;  &nbsp;  &nbsp;  &nbsp; dashboard中创建虚拟机测试，出现了ERROR错误：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/e8e9b3b3062d4fe199320451b222f148.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 在nova-scheduler.log中出现了如下的错误：

```cpp
2021-09-29 15:56:26.588 6701 ERROR nova NoMatches: No 'nova.scheduler.driver' driver found, looking for 'nova.scheduler.filterscheduler.FilterScheduler'
2021-09-29 15:56:26.588 6701 ERROR nova 
2021-09-29 15:56:29.204 6741 WARNING oslo_config.cfg [-] Deprecated: Option "scheduler_driver" from group "DEFAULT" is deprecated. Use option "driver" from group "scheduler".
2021-09-29 15:56:29.204 6741 WARNING stevedore.named [-] Could not load nova.scheduler.filterscheduler.FilterScheduler
2021-09-29 15:56:29.205 6741 CRITICAL nova [-] Unhandled error: NoMatches: No 'nova.scheduler.driver' driver found, looking for 'nova.scheduler.filterscheduler.FilterScheduler'
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在nova.conf配置文件中删除所有的filter配置：

```cpp
scheduler_driver=nova.scheduler.filterscheduler.FilterScheduler
scheduler_available_filters = nova.scheduler.filters.all_filters
scheduler_default_filters = AvailabilityZoneFilter, RamFilter, DiskFilter, ComputeFilter
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 重新在dashboard中创建虚拟机测试，再一次创建失败，下面开始查看日志：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看nova-api.log中没有error错误；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看nova-compute.log日志：

```cpp
#实例声明成功
2021-09-29 16:34:30.917 2032 INFO nova.compute.claims [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] Claim successful on node computer
2021-09-29 16:34:31.179 2032 INFO nova.virt.libvirt.driver [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] Ignoring supplied device name: /dev/vda. Libvirt can't honour user-supplied dev names
#通过之前上传的win7的qcow2格式镜像启动
2021-09-29 16:34:31.309 2032 INFO nova.virt.block_device [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] Booting with volume-backed-image d5fde846-32c9-46d7-9132-20a92e72fa90 at /dev/vda
#卷创建失败
2021-09-29 16:34:32.297 2032 WARNING nova.compute.manager [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Volume id: 034374e1-542b-4847-adad-5985b1644744 finished being created but its status is error.
#实例没有挂载到卷
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] Instance failed block device setup: VolumeNotCreated: \u5728\u7b49\u5f850 \u79d2\u62161\u5c1d\u8bd5\u540e\uff0c\u5377034374e1-542b-4847-adad-5985b1644744\u4ecd\u7136\u6ca1\u6709\u521b\u5efa\u6210\u529f\u3002\u5b83\u7684\u72b6\u6001\u662ferror\u3002
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] Traceback (most recent call last):
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/compute/manager.py", line 1912, in _prep_block_device
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     wait_func=self._await_block_device_map_created)
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/virt/block_device.py", line 882, in attach_block_devices
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     _log_and_attach(device)
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/virt/block_device.py", line 879, in _log_and_attach
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     bdm.attach(*attach_args, **attach_kwargs)
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/virt/block_device.py", line 769, in attach
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     wait_func=wait_func, image_id=self.image_id)
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/virt/block_device.py", line 367, in _create_volume
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     self._call_wait_func(context, wait_func, volume_api, vol['id'])
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/virt/block_device.py", line 724, in _call_wait_func
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     {'volume_id': volume_id, 'exc': exc})
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/oslo_utils/excutils.py", line 220, in __exit__
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     self.force_reraise()
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/oslo_utils/excutils.py", line 196, in force_reraise
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     six.reraise(self.type_, self.value, self.tb)
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/virt/block_device.py", line 714, in _call_wait_func
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     wait_func(context, volume_id)
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]   File "/usr/lib/python2.7/site-packages/nova/compute/manager.py", line 1571, in _await_block_device_map_created
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05]     volume_status=volume_status)
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] VolumeNotCreated: \u5728\u7b49\u5f850 \u79d2\u62161\u5c1d\u8bd5\u540e\uff0c\u5377034374e1-542b-4847-adad-5985b1644744\u4ecd\u7136\u6ca1\u6709\u521b\u5efa\u6210\u529f\u3002\u5b83\u7684\u72b6\u6001\u662ferror\u3002
2021-09-29 16:34:32.472 2032 ERROR nova.compute.manager [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] 
2021-09-29 16:34:34.396 2032 ERROR nova.compute.manager [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] 实例6ddd4885-6134-48ef-b5b4-17804346cb05的构建已中止：在等待0 秒或1尝试后，卷034374e1-542b-4847-adad-5985b1644744仍然没有创建成功。它的状态是error。: BuildAbortException: \u5b9e\u4f8b6ddd4885-6134-48ef-b5b4-17804346cb05\u7684\u6784\u5efa\u5df2\u4e2d\u6b62\uff1a\u5728\u7b49\u5f850 \u79d2\u62161\u5c1d\u8bd5\u540e\uff0c\u5377034374e1-542b-4847-adad-5985b1644744\u4ecd\u7136\u6ca1\u6709\u521b\u5efa\u6210\u529f\u3002\u5b83\u7684\u72b6\u6001\u662ferror\u3002
#去除该实例的virtual IP
2021-09-29 16:34:34.399 2032 INFO os_vif [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Successfully unplugged vif VIFBridge(active=False,address=fa:16:3e:d0:9b:25,bridge_name='brq9d470a6f-3b',has_traffic_filtering=True,id=1c293d4e-9e15-4cce-887d-0d12d94580b2,network=Network(9d470a6f-3b69-4105-b0eb-f6ef70a9dec3),plugin='linux_bridge',port_profile=<?>,preserve_on_delete=False,vif_name='tap1c293d4e-9e')
#将该实例分配的网络撤销
2021-09-29 16:34:36.226 2032 INFO nova.compute.manager [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 6ddd4885-6134-48ef-b5b4-17804346cb05] Took 1.83 seconds to deallocate network for instance.
2021-09-29 16:34:36.538 2032 INFO nova.scheduler.client.report [req-ce75f8ce-65b5-4266-acca-e6cf8be864ab 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Deleted allocation for instance 6ddd4885-6134-48ef-b5b4-17804346cb05
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以从日志中看到，卷创建失败，推测是compute节点的存储空间不够了，因此删除一个大的centos实例后，查看compute节点的资源情况：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/3135053133d24d188f6a90f3d2c400b4.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，compute节点原本设置的存储空间就很少（40G），可用的只有35G，设置了两个cirros实例，存储空间使用了2G，内存之前设置了8G，可用的只有7.6G，使用了640MB；

&nbsp;  &nbsp;  &nbsp;  &nbsp; controller节点设置了40G的存储空间，可用的只有35G，内存设置了4G，可用的内存空间为3.7G，使用了512MB内存。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 再次在dashboard中创建虚拟机，依然是ERROR状态，艹了，继续查看日志：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 主要是compute节点中的nova-compute.log日志的错误：

```cpp
#卷创建失败
2021-09-29 22:07:28.028 1843 ERROR nova.compute.manager [instance: dc030e38-1ad1-4318-b0df-a3df40daa4ef] VolumeNotCreated: \u5728\u7b49\u5f85132 \u79d2\u621643\u5c1d\u8bd5\u540e\uff0c\u53778e64cc5d-b20c-453e-8886-f57502ff2924\u4ecd\u7136\u6ca1\u6709\u521b\u5efa\u6210\u529f\u3002\u5b83\u7684\u72b6\u6001\u662ferror\u3002
2021-09-29 22:07:28.028 1843 ERROR nova.compute.manager [instance: dc030e38-1ad1-4318-b0df-a3df40daa4ef] 
#由于卷创建失败，实例的创建也失败了
2021-09-29 22:07:28.143 1843 ERROR nova.compute.manager [req-032b8463-1cc1-43d2-83c1-047b8d50db6e 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: dc030e38-1ad1-4318-b0df-a3df40daa4ef] 实例dc030e38-1ad1-4318-b0df-a3df40daa4ef的构建已中止：在等待132 秒或43尝试后，卷8e64cc5d-b20c-453e-8886-f57502ff2924仍然没有创建成功。它的状态是error。: BuildAbortException: \u5b9e\u4f8bdc030e38-1ad1-4318-b0df-a3df40daa4ef\u7684\u6784\u5efa\u5df2\u4e2d\u6b62\uff1a\u5728\u7b49\u5f85132 \u79d2\u621643\u5c1d\u8bd5\u540e\uff0c\u53778e64cc5d-b20c-453e-8886-f57502ff2924\u4ecd\u7136\u6ca1\u6709\u521b\u5efa\u6210\u529f\u3002\u5b83\u7684\u72b6\u6001\u662ferror\u3002
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 再次在compute节点的cinder服务的volume.log中查看日志：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 有个关键信息如下所示，我们之前创建的win7的qcow2格式镜像设置的虚拟大小为100G，而这里为该进镜像设置的卷大小只有20G，可能是这里出现了问题。

```cpp
2021-09-29 22:07:26.738 1802 ERROR cinder.volume.manager ImageUnacceptable: \u6620\u50cf d5fde846-32c9-46d7-9132-20a92e72fa90 \u65e0\u6cd5\u69f\u56e0\u662f\uff1a \u6620\u50cf\u865a\u62df\u5927\u5c0f\u4e3a 100GB\uff0c\u5728\u5927\u5c0f\u4e3a 20GB \u7684\u5377\u4e2d\u5c06\u65e0\u6cd5
2021-09-29 22:07:26.738 1802 ERROR cinder.volume.manager
```
<br>



### （3）win7虚拟机创建测试（dashboard图形化）

&nbsp;  &nbsp;  &nbsp;  &nbsp; 因此，我们将qcow2格式的win7磁盘修改为20G：

```cpp
qemu-img resize --shrink win7_xshell.qcow2 20G
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 将qcow2格式的镜像上传到glance中：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 上传qcow2格式镜像：

```cpp
glance image-create --name "win7_xshell_qcow2" --file /vhost/win7_xshell.qcow2 --disk-format qcow2 --container-format bare --visibility public --progress
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在nova.conf配置文件中删除所有的filter配置：

```cpp
scheduler_driver=nova.scheduler.filterscheduler.FilterScheduler
scheduler_available_filters = nova.scheduler.filters.all_filters
scheduler_default_filters = AvailabilityZoneFilter, RamFilter, DiskFilter, ComputeFilter
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 重新尝试通过dashboard创建虚拟机：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置flavor为2核，2G内存，20G磁盘；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可惜又出现了volume创建错误：

```cpp
Exception during message handling: ImageCopyFailure: \u672a\u80fd\u5c06\u6620\u50cf\u590d\u5236\u5230\u5377\uff1aqemu-img: error while writing sector 38797312: Input/output error
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 看起来又是volume的创建问题，镜像复制失败，回想起创建实例时是通过qcow2格式的镜像创建的，但是也选择了创建一个新的卷做持久化存储。这个选项可能就是将镜像复制到volume卷中做持久化的，这样就导致了镜像复制错误的问题。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 因此，这一次我们选择直接通过image镜像创建实例，而不创建新的卷，果然创建成功了，草草草！！！
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2cc403f29c3a4c4baeab038a3d071d17.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过控制台连接刚刚创建的虚拟机，如下所示出现了创建该win7虚拟机镜像时的文档，因此可以得出该虚拟机就是通过之前的qcow2格式镜像创建的，完全一致：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/799966efbea3427197dd7f8c2a1ec06b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_12,color_FFFFFF,t_70,g_se,x_16)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看该虚拟机的IP地址：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/a8a7b27f198e45e393b6dbf884c9a5dc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的虚拟资源使用情况：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/1af389b5595448b58facef22d5cc0bfa.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

<br>



### （4）win7虚拟机创建测试（xshell命令）

```cpp
openstack server create --image win7_xshell_qcow2 --flavor win7_xshell win7_xshell
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如下所示，虚拟机创建完毕：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/b9bc5f58efaf4321befc12f8f113f603.png)
<br>



### （5）dashboard创建并挂载卷测试

&nbsp;  &nbsp;  &nbsp;  &nbsp; 直接在dashboard中创建卷，给win7_dash虚拟机创建了一个2G的空白卷并挂载到该虚拟机上。通过控制台连接到该虚拟机，却没有发现该2G的磁盘，只有一个10G的根磁盘。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/ab659ace15514db68c1877fb640ec39d.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过cmd命令查看虚拟机中的磁盘情况：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 先调用命令diskpart；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 再执行命令list disk；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到磁盘数量如下所示：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/9071ed276c3b452286e5b5390ccebbd2.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过磁盘管理查看磁盘情况：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 控制管理器-》管理工具-》磁盘管理
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/23fb06fb558c41779f675c70b77d6170.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)



&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以手动在磁盘管理中进行磁盘的分区，选择设置为D盘，继而在计算机中显示了该D盘：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/9d8330c988cf4563af4564003daa307b.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 在D盘中创建一个文档作为标志；


&nbsp;  &nbsp;  &nbsp;  &nbsp; 对该卷进行备份测试，dashboard中直接对该卷进行分离实例，使得卷的状态为可用，尝试为该卷创建备份，将该卷上传到glance中：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/3f6f0a075c054b1188184ffeebd4f49b.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 再次通过该image镜像创建一个卷chrome_img，然后将该卷挂载到另一个实例win7_xshell中：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/1eb158b112cd4d518cde2762e0bec857.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过控制台连接该实例，竟然查看到该虚拟机的磁盘管理中除了有一个10G的根磁盘，那个2G的磁盘竟然直接显示出来作为了D盘，这难道是因为我们在win7_dash虚拟机中直接挂载了2G的磁盘后进行了备份，并且作为镜像上传到了glance中，接着通过该镜像创建新的卷chrome_img直接挂载到另一个实例win7_xshell中。新的卷不需要进行卷的分区，可以直接使用。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/793a4089315b44c0b6ef42a569412f89.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，该磁盘中有我们之前创建好的标志文档，标志着这就是我们之前创建的磁盘。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在dashboard中扩展原始的chrome卷为3G的大小，再次将该卷挂载到win7_dash实例上，连接到该实例中查看磁盘的使用情况：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/071cf18e9a0f4b279beba06c1b1f6b58.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 并通过cmd命令查看该虚拟机挂载的磁盘情况：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/43480d6392d44cc696120ddb1226bab8.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，确实挂载了3G的扩展卷，但是新增的1G卷并没有进行磁盘分区，因此无法使用，还需要手动到磁盘管理中进行分配，扩展原有的D盘为3G；
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/99deef21bdf94ad78b5f3964572b2d80.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 继续查看磁盘中的使用情况，可以看到出现了3G的磁盘：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/4ad03932e87d4bc89a0799ce321c1cf4.png)

<br>




### （6）xshell命令创建并挂载卷测试

&nbsp;  &nbsp;  &nbsp;  &nbsp; volume命令如下所示：
卷备份：

```cpp
  volume backup create
  volume backup delete
  volume backup list
  volume backup restore
  volume backup set
  volume backup show
```

卷操作：

```cpp
  volume create
  volume delete
  volume host set
  volume list
  volume migrate
```

卷qos：

```cpp
  volume qos associate
  volume qos create
  volume qos delete
  volume qos disassociate
  volume qos list
  volume qos set
  volume qos show
  volume qos unset
```

volume的分布式服务：

```cpp
  volume service list
  volume service set
```

卷设置：

```cpp
  volume set
  volume show
```

卷的快照：

```cpp
  volume snapshot create
  volume snapshot delete
  volume snapshot list
  volume snapshot set
  volume snapshot show
  volume snapshot unset
  volume transfer request accept
  volume transfer request create
  volume transfer request delete
  volume transfer request list
  volume transfer request show
```

卷的类型：

```cpp
  volume type create
  volume type delete
  volume type list
  volume type set
  volume type show
  volume type unset
  volume unset
```

创建卷：

```cpp
openstack volume create --size 2 --image chrome_img chrome_3  
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 经过一段时间的创建，创建成功了卷：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/1a684ea8cbd445c08f371db623688c01.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 将卷chrome_3挂载到实例win7_dash上：

```cpp
openstack server add volume win7_dash chrome_3
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/8a309bc7c0f74980b8355769b039943d.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，win7_dash实例中挂载了该卷到/dev/vdc中；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过控制台查看win7_dash实例，并没有看到挂载的第二个卷：

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/b2c78c99c17f4b71889ee9d3c9fc7098.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 再次通过cmd命令查看磁盘挂载情况：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/c45d3d9a882b4d089c3844329f376659.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 为啥该磁盘是脱机状态呢？

&nbsp;  &nbsp;  &nbsp;  &nbsp; 将卷chrome从win7_dash实例中脱机，可以看到该磁盘自动从实例中脱机了：

```cpp
openstack server remove volume win7_dash chrome
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9e9b5383b4704b09a127a2902f0c3d7f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

 


&nbsp;  &nbsp;  &nbsp;  &nbsp; 再次将卷chrome_3从实例win_dash中脱机，并且再次挂载，可以看到之前没有挂载显示的卷chrome_3重新显示出来了：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/d451ab41e7c743a69f5dfa3b5005feb1.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过卷chrome_3来创建image镜像chrome_img_2：

```cpp
openstack image create --container-format bare --disk-format qcow2 --volume chrome_3 chrome_img_2
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/81322ed1256c4633bb61326a2a3783af.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过该镜像chrome_img_2创建新的卷chrome_4：

```cpp
openstack volume create --size 2 --image chrome_img_2 chrome_4  
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 卷的状态是ERROR；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 重新对卷chrome_3创建新的镜像chrome_img_3：

```cpp
openstack image create --container-format bare --disk-format raw --volume chrome_3 chrome_img_3
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，该镜像的大小是2G，与之前640KB的qcow2格式对比明显：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/3d74c4c5146b4f1c9e82ac398a185c12.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 重新通过该镜像chrome_img_3创建新的卷chrome_4：

```cpp
openstack volume create --size 2 --image chrome_img_3 chrome_4  
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建的卷的状态依然是ERROR；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 因此，尝试使用如下的命令创建卷的image镜像chrome_img_2：

```cpp
cinder upload-to-image --disk-format raw --visibility shared chrome_3 chrome_img_2   
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b3529d6d0dc14d07aa228f4ab35326c0.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 再次通过镜像创建新的卷chrome_4

```cpp
openstack volume create --size 2 --image chrome_img_2 chrome_4
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 状态依然失败，算了，不试了艹，估计做卷的备份的地方应该不多；
<br>



### （7）win机制作指定内容的vhd格式虚拟磁盘

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在磁盘管理中创建新的vhd格式的虚拟磁盘，显示为新的E盘；

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/f1a69ed94fcc43f6b1c2b31b295e85c0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 将需要传输的文件装入新的虚拟磁盘E中，然后将该磁盘从win机中分离：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 将该vhd格式文件上传到Controller节点中，然后上传到glance中作为镜像：

```cpp
glance image-create --name "chrome_vhd" --file /image/chrome.vhd --disk-format vhd --container-format bare --visibility public --progress
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/f087ed9879544c0baafcc2673fc819df.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 再通过该vhd格式镜像创建卷：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/bfab301dc9094525be02cdba6a7420b8.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 将该卷挂载到实例win7_dash中，通过控制台连接实例查看：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/34c3b53694754323a8e51f353bf08912.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，该卷直接挂载到了实例上，并且可以直接查看使用。
<br>



### （8）指定软件卷的制作

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在Win机中创建两个vhd格式虚拟磁盘，将指定的exe程序放入虚拟磁盘中，并将该磁盘用上面的方式上传到glance中作为卷：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/e2fee30ff73647c2a6b9ed8401725439.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 接着通过该镜像创建卷：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/9204316b321045f09e897af1c4a00f2a.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以直接挂载到实例上；
<br>



### （9）控制节点程序嵌入虚拟机镜像测试

&nbsp;  &nbsp;  &nbsp;  &nbsp; 微服务都是java程序，因此需要在虚拟机win7_dash中安装JDK以及IDEA；
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/cb13bd4758fa40ec9a5c4a7452d19bfd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_17,color_FFFFFF,t_70,g_se,x_16)












