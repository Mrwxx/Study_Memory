@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 1.	镜像类型
&nbsp;  &nbsp;  &nbsp;  &nbsp; 一类是 物理机镜像格式。 ISO BIN IMAGE等。使用此类镜像创建虚拟机，需从头安装该操作系统。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 一类是 虚拟机镜像格式。 raw qcow2 vmdk等。已经安装好了系统并进行了基础设置，可看做一个模板。

&nbsp;  &nbsp;  &nbsp;  &nbsp; OpenStack 上传到glance的镜像既可以是ISO等格式的物理机镜像 也可以是qcow2等格式的虚拟机镜像。

有如下的格式

```cpp
ISO 
Raw
Machine (kernel/ramdisk outside of image, a.k.a. AMI)
VHD (Hyper-V)
VDI (VirtualBox)
qcow2 (Qemu/KVM)
VMDK (VMWare)
OVF (VMWare, others)
```
<br>




## 2.	容器格式

&nbsp;  &nbsp;  &nbsp;  &nbsp; 容器container格式是指虚拟机映像是否包含一个文件格式，该文件格式还包含有关实际虚拟机的元数据。需要注意的是：容器格式字符串在当前并不会被glance或其他OpenStack组件的使用，所以如果你不确定，简单的将容器格式指定bare是安全的。
<br>



## 3.OpenStack镜像上传方式

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以通过dashboard上传，也可以使用openstack的命令上传。镜像名称可重复，根据ID查找镜像。如上传WindowsServer2008 的镜像（ISO和qcow2两种格式），不同格式的镜像上传时应选择对应的格式。
<br>



## 4.	（未实施）dashboard创建镜像

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在dashboard的镜像界面中，通过创建镜像的方式创建ISO格式和QCOW2格式的镜像，如下所示：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/d0d93073fc2446ddbcd62836b973e32d.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过openstack命令行来查看image镜像：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/9a0058a8751a4accb9851587075aee73.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看Image对应的物理文件，在默认的images存储目录/var/lib/glance/images下：

```cpp
ll /var/lib/glance/images
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2f90534f01204a239006f86e9732f51a.png)
<br>



 
## 5.（未实施）glance创建镜像命令

```cpp
glance image-create命令 可以使用以下参数：
--id <IMAGE_ID> 　　　　　　 　　　　　　 　　　　#镜像的ID
--name <NAME> 　　　　 　　 　　　　 　　　　　　#镜像的名称
--store <STORE> 　　　　　　 　　　　 　　　　　　#储存的镜像上传到
--disk-format <DISK_FORMAT>　　　　　　  　　　    #镜像的格式。可以接受的格式包含: ami,ari, aki, vhd, vmdk, raw, qcow2, vdi, and iso.
--container-format <CONTAINER_FORMAT>　     　　  #镜像容器的格式。可以接受的格式包含:ami,ari, aki, bare, and ovf.
--owner <TENANT_ID> 　　　　　　　　　　　　 　　# 拥有该镜像的租户
--size <SIZE>　　　　　　　　　　　　　　　　 　　  #镜像的大小(以bytes表示). 一般只与'--location'和'--copy_from'一起使用。
--min-disk <DISK_GB> 　　　　　　　　　　　　 　　 #启动镜像所需的最小硬盘空间(用gigabytes表示).
--min-ram <DISK_RAM> 　　　　　　　　　　　　 　  #启动镜像所需的最小内存数量(用megabytes表示).
--location <IMAGE_URL>　　　　　　　　　　　　　   #镜像所在位置的URL。例如，如果镜像储存在swift中，
--file <FILE> 　　　　　　　　　　　　　　　　 　　    #在创建过程中将要被上传的本地文件（包括硬盘镜像）。另外，镜像也可以通过stdin传递给客户端。
--checksum <CHECKSUM>　　　　　　　　　　　　　#被Glance使用的可用于认证的镜像数据的哈希值，在此请提供一个md5校验值。
--copy-from <IMAGE_URL>　　　　　　　　　　　　    #用法和'--location'参数相似，但表明Glance服务器应该能立即从镜像所储存的地方拷贝数据并储存。
--is-public [True|False]　　　　　　　　　　　　　　　  #表示镜像是否能被公众访问。
--is-protected [True|False]　　　　　　　　　　　　　　#用于避免镜像被删除。
--property <key=value>　　　　　　　　　　　　　　　#与镜像有关的任意的属性。可以使用很多次。
--human-readable 　　　　　　　　　　　　　　　　    #用对人友好的格式打印镜像的尺寸。
--progress 　　　　　　　　　　　　　　　　　　　　  #显示上传的进度条
```


  首先获得admin权限

```cpp
source admin-openrc
```

上传ISO格式镜像：

```cpp
glance image-create --name "win7_xshell_iso" --file /root/image/ cn_windows_7_ultimate_with_sp1_x64_dvd_u_677408.iso --disk-format iso --container-format bare --visibility public --progress
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/715ade15f92d4a2cb14a19913725c3d0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

 



上传qcow2格式镜像：

```cpp
glance image-create --name "win7_xshell_qcow2" --file /root/image/ cn_windows_7_ultimate_with_sp1_x64_dvd_u_677408.iso --disk-format qcow2 --container-format bare --visibility public --progress
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/e0ad2e55c36f4a459b6ad5bb524efbff.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)
<br>




## 6.部署kvm基本环境

安装kvm核心包——虚拟操作系统模拟器加速模块

```cpp
yum -y install qemu-kvm qemu-kvm-tools
```

安装kvm管理工具

```cpp
yum -y install libvirt libvirt-python libguestfs-tools
```

安装镜像安装程序virt-install

```cpp
yum install -y virt-install
```

安装vncserver。使用vnc viewer连接完成虚拟机的图形化安装。

```cpp
yum -y install tigervnc-server
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 虚拟机安装系统时会使用图形界面安装，主机安装vncserver，安装虚拟机时设置vnc client等远程连接虚拟机的端口，如5900,5901,5902，然后通过vnc client连接虚拟机进行系统的安装。

&nbsp;  &nbsp;  &nbsp;  &nbsp; vnc client用于远程连接vnc server。可以是windows下软件。如VNC viewer。viewer连接vnc server时需要保证服务器主机防火墙相关端口开放或者，暂时关闭防火墙。

```cpp
systemctl stop firewalld
或是：
firewall-cmd --zone=public --add-port=5900/tcp --permanent
firewall-cmd --reload
```

启动libvirtd管理程序：

```cpp
systemctl enable libvirtd
systemctl start libvirtd
```

<br>



## 7.Centos系统qcow2格式镜像制作
### （1）给虚拟机创建一个网络

&nbsp;  &nbsp;  &nbsp;  &nbsp; 手动创建镜像需要确保libvirt运行有default网络，这个网络可以给虚拟机提供上网服务。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前是否启用default网络

```cpp
virsh net-list
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/c00718011adf49048910b06c63afd12e.png)


注：如果没有启用，使用以下命令启用default

```cpp
 virsh net-start default
```
<br>



### （2）创建虚拟机

&nbsp;  &nbsp;  &nbsp;  &nbsp; 将iso镜像文件上传到/image目录下：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/83db24741d9b4b47a75b48f158c85054.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建一个20G的qcow2格式的磁盘文件给虚拟机使用

```cpp
qemu-img create -f qcow2 /vhost/centos7_x86_64.qcow2 20G
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 安装centos7虚机

```cpp
virt-install --virt-type kvm --name centos7_x86_64 --ram 2048 --disk /vhost/centos7_x86_64.qcow2,format=qcow2 --network network=default --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux --location=/image/CentOS-7-x86_64-DVD-2009.iso
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/e7bbc600cbf744499a71f9065f6dd1c4.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看刚刚通过kvm创建的虚机端口：

```cpp
netstat -lntup | grep kvm
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/670a152f8e70425abe84eb0d2a043720.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 因此，可以推出当前的虚机连接地址为：192.168.112.146:5900，通过vnc client客户端连接到该地址中：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/a07bcfb4c97f4f8195e49cfcbd37240f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 按照centos的安装方式安装minimal最小版本即可。

```cpp
账户：root
密码：wangdage980204
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在/root目录内创建一个test1文件，以检查后续再次通过该qcow2镜像创建虚机时是否为原来的镜像。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注：只有配置了KVM虚拟机，libvirt就会生成一个与操作系统对应的xml文件，其记录了kvm虚拟机的状态。路径如下：/etc/libvirt/qemu/CentOS-6.6-x86_64.xml  
&nbsp;  &nbsp;  &nbsp;  &nbsp; 注：此文件只能通过“virsh edit”命令修改

 
![在这里插入图片描述](https://img-blog.csdnimg.cn/171cdc73f09f4787bc7de641cd80a3b2.png)

<br>




### （3）启动与测试虚机

查看当前的虚机情况：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/ee898976345d461db405ea6929c46c6d.png)


启动虚机：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/5115c96da30742ff84812499f2e2f62f.png)

关闭该虚机后，备份qcow2格式的镜像：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/e80078c4eb714ac6b05b3759cf61633e.png)

<br>




### （4）Controller节点创建镜像到glance中

&nbsp;  &nbsp;  &nbsp;  &nbsp; 将刚刚制作好的qcow2格式的centos7镜像上传到glance中：

&nbsp;  &nbsp;  &nbsp;  &nbsp;   首先获得admin权限

```cpp
source /scripts/admin-openrc
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 上传镜像到glance中：

```cpp
openstack image create --file /vhost/centos7_x86_64.qcow2 --disk-format qcow2 --container-format bare --public centos7_x86_64
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/22f75c2c56bf45a0865d83d2d358c728.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的image镜像情况：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/0b463852cc1248128e7eff01f8f57182.png)

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看Image对应的物理文件，在默认的images存储目录/var/lib/glance/images下：

```cpp
ll /var/lib/glance/images
```

<br>




### （5）通过dashboard测试创建实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过dashboard的镜像界面创建一个实例，但是首先需要创建一个实例类型
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/211634c9fd714fe6a13e4f52193407dc.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们设定为2核，2G内存，20G磁盘的实例类型。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2e76ec1e7e2849c4a5d24de129a61ead.png)

&nbsp;  &nbsp;  &nbsp;  &nbsp; 然后通过之前上传的centos7镜像创建一个实例：
 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们可以通过控制台连接到该实例中，查看该实例中的test1文件是否存在，如果存在则说明是之前创建的qcow2格式的镜像：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/a3e067f3e8db41d38ca1a26ca53d40aa.png)
<br>




### （6）通过命令创建单实例

```cpp
openstack server create --image centos7_x86_64 --flavor centos7 centos7_2
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/0e245bc3cb4648fba80ca69b5030dfdc.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过命令行创建的方式更加稳定，通过web界面会出现多次创建失败的问题。
<br>



### （7）通过同一个镜像创建新的实例

```cpp
openstack server create --image centos7_x86_64 --flavor centos7 centos7_2
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 很明显，通过同一个镜像创建新的实例是失败的：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/7acfb36058684af58949ceb75d585925.png)
<br>




### （8）复制新的镜像创建新的实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 从原有的qcow2格式的镜像中向glance中提交新的qcow2格式的镜像

```cpp
openstack image create --file /vhost/centos7_x86_64.qcow2 --disk-format qcow2 --container-format bare --public centos7_x86_64_2
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ca0ec74809154a19b3f9b503004cf756.png)

 

```cpp
openstack server create --image centos7_x86_64_2 --flavor centos7 centos7_2
```

结果依然是创建失败：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/588a2eaed3eb46bea7000b0e6a78bc62.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 这是因为不能从同一个qcow2格式的镜像上传到glance中作为两个镜像吗？我们通过同一个qcow2格式的镜像向glance中提交了两个镜像，第一个镜像是创建成功的，第二个创建失败的。
<br>



### （9）复制qcow2格式镜像重新上传到glance中

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/eff0e53285554927a7e8bd06999b8b05.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过新的qcow2格式镜像提交到glance中：

```cpp
openstack image create --file /vhost/centos7_x86_64_2.qcow2 --disk-format qcow2 --container-format bare --public centos7_x86_64_2
```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2f0f4b470e5a44e1abc1dae982f3839e.png)



```cpp
openstack server create --image centos7_x86_64_2 --flavor centos7 centos7_2
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/453a132bc35f4fd585aa0ac2ac173151.png)
<br>



 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 依然创建失败，这两个qcow2格式的文件都不一样，为啥还出错呢？


### （10）在qcow2格式基础镜像的基础上创建新的qcow2格式的镜像

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的qcow2格式的镜像：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/3463edd37b8443ba8313ed8e7e2e7e0d.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看出，当前的qcow2格式的镜像文件的虚拟大小为20G，磁盘大小为1.6G。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 以该镜像为基础镜像创建新的qcow2格式的镜像：

```cpp
qemu-img create -b centos7_x86_64.qcow2 -f  qcow2   centos7_x86_64_2.qcow2
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看该新镜像的信息：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/06f7fdc0053844f589cd8b3ff4d83a49.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，发现该镜像是引用基础的镜像centos_x86_64.qcow2，同时它的磁盘大小也只有196k。这是增量镜像，类似于增量备份，或者copy on write。首先我们创建一个镜像文件A，在A上安装一个全新的操作系统。之后通过镜像A创建一个镜像B，从这一刻开始，A上只会发生读操作，数据写入到B镜像中。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 因此，基础镜像是不能删除的，删除了之后以该基础镜像为基础的其他镜像都会失效。
<br>



### （11）通过该新的增量镜像上传到glance中


&nbsp;  &nbsp;  &nbsp;  &nbsp; 上传镜像到glance中：

```cpp
openstack image create --file /vhost/centos7_x86_64_2.qcow2 --disk-format qcow2 --container-format bare --public centos7_x86_64_2
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c67eba51813741869d5793af78adc281.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5byA5Y-R5bCP6bi9,size_19,color_FFFFFF,t_70,g_se,x_16)
<br>



 

### （12）测试通过该新的镜像创建实例


```cpp
openstack server create --image centos7_x86_64_2 --flavor centos7 centos7_2
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 同样是创建失败？这是为什么呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp; 思考发现，使用的是centos7这个flavor，这个flavor的格式是2G，2核CPU，20G的磁盘，我的Compute节点使用的是4G内存，因此这个内存空间是不足的，所以创建总是会失败。

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/8fbb312f0ebd45599e79cda1a974d7d2.png)
<br>




### （13）创建新的flavor后继续创建虚机

```cpp
openstack flavor create --ram 1024 --disk 20 --vcpus 1 --id 3 centos72
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/da3c95b053fe47ddb06363acdc6d7939.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看当前的flavor的列表：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/f184bd008a8e4ef0b0ba6a193b8b80cc.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 重新尝试创建虚机，但是使用的是新的flavor：

```cpp
openstack server create --image centos7_x86_64_2 --flavor centos72  centos7_new
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/af3397cf14164e3db15102589bc413cd.png)

 

&nbsp;  &nbsp;  &nbsp;  &nbsp; 依然出现error状态；

<br>



### （14）查看nova日志
&nbsp;  &nbsp;  &nbsp;  &nbsp; Controller节点和Compute节点中都有nova日志，首先查看Controller节点中的nova日志：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2eea3f5ee08e4eb88014f024e0465386.png)


&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们需要对nova-api.log日志好好分析一下：

如下命令
openstack server list，查询实例列表

&nbsp;  &nbsp;  &nbsp;  &nbsp; 对应着nova-api.log中的如下两行，一行是查询server，另一行是查询flavor：

```cpp
2021-09-17 19:24:30.891 2252 INFO nova.osapi_compute.wsgi.server [req-0c90b8ee-66eb-4ca9-9c4b-d3e465211ec6 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/detail HTTP/1.1" status: 200 len: 4801 time: 0.6548059
2021-09-17 19:24:30.949 2252 INFO nova.osapi_compute.wsgi.server [req-9afe362e-35e6-451c-bb98-21ead6db2dd3 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/detail?is_public=None HTTP/1.1" status: 200 len: 1479 time: 0.0143611
```

删除实例的命令：
openstack server delete centos7_new，对应如下的日志：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 删除一个实例对应的nova-api中的日志为如下所示，首先查找的是centos7_new实例，为啥没有找到？
&nbsp;  &nbsp;  &nbsp;  &nbsp; 然后通过get方法查询/v2.1/servers/centos7_new，状态码是404说明没有找到；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 然后通过get方法查询/v2.1/servers?name=centos7_new，状态码是200，说明找到了；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 然后通过get方法查询/v2.1/servers/7ac8e2f0-0bb1-4e48-b0cf-00de7e050c1c，后面的已传字符是实例的编号，状态码是200，说明找到了；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 最后通过delete方法删除/v2.1/servers/7ac8e2f0-0bb1-4e48-b0cf-00de7e050c1c，状态码是204，说明没有返回内容；


```cpp
2021-09-17 19:33:35.449 2255 INFO nova.api.openstack.wsgi [req-feb51187-0712-4ebe-9861-0b060e135a56 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 实例 centos7_new 没有找到。
2021-09-17 19:33:35.450 2255 INFO nova.osapi_compute.wsgi.server [req-feb51187-0712-4ebe-9861-0b060e135a56 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/centos7_new HTTP/1.1" status: 404 len: 522 time: 0.2160928
2021-09-17 19:33:35.561 2255 INFO nova.osapi_compute.wsgi.server [req-65e9a8f7-0ee7-4ab3-968d-3a33653aa71b 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers?name=centos7_new HTTP/1.1" status: 200 len: 705 time: 0.1083212
2021-09-17 19:33:35.645 2255 INFO nova.osapi_compute.wsgi.server [req-3ab6a065-810a-4cbc-8a8b-09124c4da408 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/7ac8e2f0-0bb1-4e48-b0cf-00de7e050c1c HTTP/1.1" status: 200 len: 4267 time: 0.0799270
2021-09-17 19:33:35.742 2255 WARNING nova.compute.api [req-55be22b0-898b-426f-b382-119f60c43d74 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 7ac8e2f0-0bb1-4e48-b0cf-00de7e050c1c] instance's host None is down, deleting from database
2021-09-17 19:33:36.456 2255 INFO nova.osapi_compute.wsgi.server [req-55be22b0-898b-426f-b382-119f60c43d74 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "DELETE /v2.1/servers/7ac8e2f0-0bb1-4e48-b0cf-00de7e050c1c HTTP/1.1" status: 204 len: 405 time: 0.8070309
```

创建虚机的命令如下所示：

```cpp
openstack server create --image centos7_x86_64_2 --flavor centos72  centos7_new
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查询flavor的list，状态码是200；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 查询云主机类型centos72，却没有找到；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过get方法查询/v2.1/flavors/centos72，状态码是404说明没有找到；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过get方法查询/v2.1/flavors，状态码是200；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过post方法上传实例到/v2.1/servers，状态码是202表示是异步处理；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过get方法查询server状态/v2.1/servers/a275a604-84f0-43f4-98d1-7cc3bbbb0903，状态码是200；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过get方法查询指定的id为3的flavor，/v2.1/flavors/3，状态码是200；


```cpp
2021-09-17 19:51:48.612 2254 INFO nova.osapi_compute.wsgi.server [req-196b6d50-aed7-4476-bc53-59ad2f6d96a9 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/detail HTTP/1.1" status: 200 len: 1479 time: 0.2345939
2021-09-17 20:01:58.911 2253 INFO nova.api.openstack.wsgi [req-ce277c0e-ac19-4358-bf26-c67ac8199a52 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 云主机类型 centos72 没有找到。
2021-09-17 20:01:58.912 2253 INFO nova.osapi_compute.wsgi.server [req-ce277c0e-ac19-4358-bf26-c67ac8199a52 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/centos72 HTTP/1.1" status: 404 len: 537 time: 0.0172770
2021-09-17 20:01:58.928 2253 INFO nova.osapi_compute.wsgi.server [req-707e4e40-0561-48b4-896d-646d1cd7bd59 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors HTTP/1.1" status: 200 len: 965 time: 0.0131981
2021-09-17 20:01:58.942 2253 INFO nova.osapi_compute.wsgi.server [req-a1ec74e0-1377-4c77-ac4e-0ca6ff1be1b8 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/3 HTTP/1.1" status: 200 len: 766 time: 0.0103431
2021-09-17 20:01:59.407 2253 INFO nova.osapi_compute.wsgi.server [req-63b82c1f-1f5e-492e-9b66-dc761f45164c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "POST /v2.1/servers HTTP/1.1" status: 202 len: 876 time: 0.4548001
2021-09-17 20:01:59.457 2253 INFO nova.osapi_compute.wsgi.server [req-5f961569-c5ff-4b0f-aefb-bc3e36d452fd 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/a275a604-84f0-43f4-98d1-7cc3bbbb0903 HTTP/1.1" status: 200 len: 1691 time: 0.0470769
2021-09-17 20:01:59.484 2253 INFO nova.osapi_compute.wsgi.server [req-526091aa-08ee-4c28-9df1-c0d531ac94d5 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/3 HTTP/1.1" status: 200 len: 766 time: 0.0102990
```
<br>




### （15）查看image日志

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在Controller节点查看/var/log/glance/api.log日志，即镜像的日志，得到如下的结果：
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过get方法查询/v2/images/centos7_x86_64_2，即使用的image镜像是什么；

```cpp
2021-09-17 20:01:58.868 2200 INFO eventlet.wsgi.server [req-837eac44-cc2f-4f8e-9913-796cc0721877 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:01:58] "GET /v2/images/centos7_x86_64_2 HTTP/1.1" 404 450 0.238023
2021-09-17 20:01:58.885 2200 INFO eventlet.wsgi.server [req-9ff3b12d-d3b8-4443-b7ce-b2a8f21d3581 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:01:58] "GET /v2/images?limit=20 HTTP/1.1" 200 2637 0.015316
2021-09-17 20:01:58.890 2200 INFO eventlet.wsgi.server [req-2cb129aa-d608-4b6a-8508-2d4fa267ce69 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:01:58] "GET /v2/schemas/image HTTP/1.1" 200 6925 0.002258
2021-09-17 20:01:59.122 2200 INFO eventlet.wsgi.server [req-8fce43e8-3b25-41fe-88d7-8bec729187bd 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:01:59] "GET /v2/images/6e6047e0-7ecc-49d2-aa17-79cacff593af HTTP/1.1" 200 986 0.149315
2021-09-17 20:01:59.127 2200 INFO eventlet.wsgi.server [req-d15b7265-b4c4-409f-8c36-bdd11e8c6e68 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:01:59] "GET /v2/schemas/image HTTP/1.1" 200 6925 0.002797
2021-09-17 20:01:59.471 2200 INFO eventlet.wsgi.server [req-e1b2226b-62fa-42ba-90b8-1366f82d7356 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:01:59] "GET /v2/images/6e6047e0-7ecc-49d2-aa17-79cacff593af HTTP/1.1" 200 986 0.010701
2021-09-17 20:02:07.870 2200 INFO eventlet.wsgi.server [req-d23793ee-fafb-40fe-a13a-88347aaa1d6b 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:02:07] "GET /v2/images?limit=20 HTTP/1.1" 200 2637 0.015508
2021-09-17 20:02:07.874 2200 INFO eventlet.wsgi.server [req-47401128-8408-45a4-9a75-dd14d27088b1 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 - - [17/Sep/2021 20:02:07] "GET /v2/schemas/image HTTP/1.1" 200 6925 0.002249
```

<br>



### （16）根据nova创建server实例的日志流程排错

&nbsp;  &nbsp;  &nbsp;  &nbsp; 客户向nova-api发送请求，请求创建虚拟机；

&nbsp;  &nbsp;  &nbsp;  &nbsp; nova-api对请求作出处理后，向mq发送消息“让scheduler创建虚机”；

&nbsp;  &nbsp;  &nbsp;  &nbsp; nova-scheduler从mq中获取到API发送给它的消息，然后执行调度算法，从若干个计算节点中选出节点A；

&nbsp;  &nbsp;  &nbsp;  &nbsp; noav-scheduler向mq发送消息“在节点A上创建虚拟”；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 计算节点A的nova-compute从mq中获取到nova-scheduler发给它的消息，然后在本节点的Hypervisor上启动虚拟机；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在虚拟机创建的过程中，计算节点A的nova-compute如果需要查询或更新数据库消息，会通过mq向nova-conductor发送消息，nova-conductor负责数据库访问。


&nbsp;  &nbsp;  &nbsp;  &nbsp; 执行创建server实例的命令：

```cpp
openstack server create --image centos7_x86_64_2 --flavor centos72  centos7_new
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 首先查看Controller节点的nova-api.log日志：

```cpp
2021-09-17 23:48:46.780 2253 INFO nova.api.openstack.wsgi [req-32b257bf-2ff5-4b94-9213-1072fa557b8c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 云主机类型 centos72 没有找到。
2021-09-17 23:48:46.781 2253 INFO nova.osapi_compute.wsgi.server [req-32b257bf-2ff5-4b94-9213-1072fa557b8c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/centos72 HTTP/1.1" status: 404 len: 537 time: 0.0147262
2021-09-17 23:48:46.799 2253 INFO nova.osapi_compute.wsgi.server [req-851da075-eb20-4924-a170-1f58753967ad 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors HTTP/1.1" status: 200 len: 965 time: 0.0149779
2021-09-17 23:48:46.812 2253 INFO nova.osapi_compute.wsgi.server [req-2a3944b4-a89d-4f6c-9cbe-234442da470d 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/3 HTTP/1.1" status: 200 len: 766 time: 0.0095580
2021-09-17 23:48:47.149 2253 INFO nova.osapi_compute.wsgi.server [req-ba7cca9e-6c0d-4a8e-b657-60673456a472 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "POST /v2.1/servers HTTP/1.1" status: 202 len: 876 time: 0.3276680
2021-09-17 23:48:47.374 2253 INFO nova.osapi_compute.wsgi.server [req-9989529b-317e-4998-9eff-14c4e69e6aa4 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/4f75fd35-c53c-4feb-90bf-391996dee667 HTTP/1.1" status: 200 len: 1691 time: 0.2202811
2021-09-17 23:48:47.426 2253 INFO nova.osapi_compute.wsgi.server [req-e151852b-8b42-4f52-88e7-95369c3fc61a 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/3 HTTP/1.1" status: 200 len: 766 time: 0.0206590
2021-09-17 23:49:03.443 2255 INFO nova.osapi_compute.wsgi.server [req-a78a63b6-06a6-4238-ac77-1472c2d67d15 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/flavors/detail HTTP/1.1" status: 200 len: 1479 time: 0.8354931
2021-09-17 23:49:03.536 2255 INFO nova.osapi_compute.wsgi.server [req-19ee3aa7-264d-4bce-8efd-de09763f1b1c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1 HTTP/1.1" status: 302 len: 317 time: 0.0038280
2021-09-17 23:49:03.543 2255 INFO nova.osapi_compute.wsgi.server [req-6ae78456-5d4a-42d9-af14-e1a30fb18521 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/ HTTP/1.1" status: 200 len: 788 time: 0.0038471
2021-09-17 23:49:05.096 2254 INFO nova.osapi_compute.wsgi.server [req-0a1dfa3a-d832-4c44-abf0-716803a29475 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/servers/detail?limit=21&project_id=a7d812868cb74f1a978035530f55f1d0&sort_dir=desc&sort_dir=desc&sort_dir=desc&sort_key=created_at&sort_key=display_name&sort_key=uuid HTTP/1.1" status: 200 len: 6270 time: 1.4020071
2021-09-17 23:49:05.596 2255 INFO nova.osapi_compute.wsgi.server [req-145b6db0-aaf5-4487-b2b9-ebcd7f17b8c4 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/extensions HTTP/1.1" status: 200 len: 23101 time: 0.0046902
2021-09-17 23:49:05.863 2255 INFO nova.osapi_compute.wsgi.server [req-f9863cf3-9641-432e-ac53-a5277a94c51d 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/limits?reserved=1 HTTP/1.1" status: 200 len: 913 time: 0.0407920
2021-09-17 23:49:06.368 2255 INFO nova.osapi_compute.wsgi.server [req-c16f1023-d3c7-4183-a7bd-3590b7fec868 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/limits?reserved=1 HTTP/1.1" status: 200 len: 913 time: 0.0380440
2021-09-17 23:49:44.228 2255 INFO nova.osapi_compute.wsgi.server [req-876229d8-50ff-4f1c-8596-c6f40bab9ab8 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/flavors/detail HTTP/1.1" status: 200 len: 1479 time: 0.2233560
2021-09-17 23:49:44.274 2255 INFO nova.osapi_compute.wsgi.server [req-73d47f3c-629e-4316-be5d-bfe65767581d 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1 HTTP/1.1" status: 302 len: 317 time: 0.0026519
2021-09-17 23:49:44.279 2255 INFO nova.osapi_compute.wsgi.server [req-9f7572d8-afbe-448a-a025-2feb5a0921f2 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/ HTTP/1.1" status: 200 len: 788 time: 0.0028460
2021-09-17 23:49:45.046 2255 INFO nova.osapi_compute.wsgi.server [req-a72117ca-daba-424f-84b7-3d15c94ae72c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/servers/detail?limit=21&project_id=a7d812868cb74f1a978035530f55f1d0&sort_dir=desc&sort_dir=desc&sort_dir=desc&sort_key=created_at&sort_key=display_name&sort_key=uuid HTTP/1.1" status: 200 len: 6270 time: 0.6647730
2021-09-17 23:49:45.553 2255 INFO nova.osapi_compute.wsgi.server [req-077ed365-0a58-4981-9523-dad0fc98237c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/extensions HTTP/1.1" status: 200 len: 23101 time: 0.0035529
2021-09-17 23:49:45.768 2255 INFO nova.osapi_compute.wsgi.server [req-bbadcf04-abca-47f0-b067-8d3a7ee7b64c 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/limits?reserved=1 HTTP/1.1" status: 200 len: 913 time: 0.0294020
2021-09-17 23:49:46.243 2255 INFO nova.osapi_compute.wsgi.server [req-4901aa95-fb27-4db4-924e-e7845a45268d 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.145 "GET /v2.1/limits?reserved=1 HTTP/1.1" status: 200 len: 913 time: 0.0318770
```


&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看Controller节点的nova-scheduler.log日志：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 首先通过compute_filter过滤器，通过ram内存过滤，首先通过localhost.localdomain节点进行过滤；

```cpp
2021-09-17 23:48:48.256 2570 WARNING nova.scheduler.filters.compute_filter [req-ba7cca9e-6c0d-4a8e-b657-60673456a472 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] (localhost.localdomain, localhost.localdomain) ram: 3258MB disk: 32768MB io_ops: 0 instances: 0 has not been heard from in a while
2021-09-17 23:48:48.263 2570 INFO nova.filters [req-ba7cca9e-6c0d-4a8e-b657-60673456a472 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filter ComputeFilter returned 0 hosts
2021-09-17 23:48:48.263 2570 INFO nova.filters [req-ba7cca9e-6c0d-4a8e-b657-60673456a472 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filtering removed all hosts for the request with instance ID '4f75fd35-c53c-4feb-90bf-391996dee667'. Filter results: ['AvailabilityZoneFilter: (start: 1, end: 1)', 'ComputeFilter: (start: 1, end: 0)']
```


&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看Controller节点的nova-conductor.log日志：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 主要的问题是：找不到有效主机，原因是 没有足够的主机可用，猜测可能是CPU，内存，硬盘可能不足；

```cpp
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager [req-ba7cca9e-6c0d-4a8e-b657-60673456a472 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Failed to schedule instances: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
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
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager Traceback (most recent call last):
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 1379, in schedule_and_build_instances
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     instance_uuids, return_alternates=True)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 839, in _schedule_instances
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     return_alternates=return_alternates)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/client/query.py", line 42, in select_destinations
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     instance_uuids, return_objects, return_alternates)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/rpcapi.py", line 160, in select_destinations
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     return cctxt.call(ctxt, 'select_destinations', **msg_args)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/client.py", line 181, in call
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     transport_options=self.transport_options)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/transport.py", line 129, in _send
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     transport_options=transport_options)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 674, in send
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     transport_options=transport_options)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 664, in _send
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     raise result
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager Traceback (most recent call last):
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/server.py", line 235, in inner
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     return func(*args, **kwargs)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/manager.py", line 214, in select_destinations
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     allocation_request_version, return_alternates)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 96, in select_destinations
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     allocation_request_version, return_alternates)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 265, in _schedule
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     claimed_instance_uuids)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 302, in _ensure_sufficient_hosts
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager     raise exception.NoValidHost(reason=reason)
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager NoValidHost: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.310 2369 ERROR nova.conductor.manager 
2021-09-17 23:48:48.416 2369 WARNING nova.scheduler.utils [req-ba7cca9e-6c0d-4a8e-b657-60673456a472 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Failed to compute_task_build_instances: 找不到有效主机，原因是 没有足够的主机可用。
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
2021-09-17 23:48:48.417 2369 WARNING nova.scheduler.utils [req-ba7cca9e-6c0d-4a8e-b657-60673456a472 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 4f75fd35-c53c-4feb-90bf-391996dee667] Setting instance to ERROR state.: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
```
<br>



### （17）增大Compute节点的内存和CPU容量

&nbsp;  &nbsp;  &nbsp;  &nbsp; 扩大Compute节点的CPU到6核，内存到8G；

删除失败的节点：

```cpp
openstack server delete centos7_new
```

重新创建server：

```cpp
openstack server create --image centos7_x86_64_2 --flavor centos72  centos7_new
```

依然创建实例失败；

通过日志排查问题：
&nbsp;  &nbsp;  &nbsp;  &nbsp; 首先查看nova-api.log日志，如下所示并没有发现特别异常的地方：

```cpp
2021-09-18 11:01:03.995 2295 INFO nova.api.openstack.wsgi [req-35135749-7627-414f-a6d9-4ce248e3906f 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] HTTP exception thrown: 云主机类型 centos72 没有找到。
2021-09-18 11:01:03.997 2295 INFO nova.osapi_compute.wsgi.server [req-35135749-7627-414f-a6d9-4ce248e3906f 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/centos72 HTTP/1.1" status: 404 len: 537 time: 0.0310740
2021-09-18 11:01:04.048 2295 INFO nova.osapi_compute.wsgi.server [req-ff217a3e-5911-4bd1-98a0-a2c18c67f749 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors HTTP/1.1" status: 200 len: 965 time: 0.0419452
2021-09-18 11:01:04.085 2295 INFO nova.osapi_compute.wsgi.server [req-da335951-60ca-41bf-b1fc-b24fbd76b8c7 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/3 HTTP/1.1" status: 200 len: 766 time: 0.0295641
2021-09-18 11:01:05.811 2295 INFO nova.osapi_compute.wsgi.server [req-fbf96a04-09d5-4e3d-bf35-9b0935969296 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "POST /v2.1/servers HTTP/1.1" status: 202 len: 876 time: 1.6947610
2021-09-18 11:01:05.962 2295 INFO nova.osapi_compute.wsgi.server [req-b6cae590-65b7-4fc6-948c-acf3d7a1c23b 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/servers/2d43331b-bcff-43af-be11-5c3eefd8b7a5 HTTP/1.1" status: 200 len: 1691 time: 0.1441481
2021-09-18 11:01:06.046 2295 INFO nova.osapi_compute.wsgi.server [req-914736ca-0b90-4628-ac4b-f53c92653224 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] 192.168.112.146 "GET /v2.1/flavors/3 HTTP/1.1" status: 200 len: 766 time: 0.0342619
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 继续排查nova-scheduler.log日志，

```cpp
2021-09-18 11:01:09.117 2629 WARNING nova.scheduler.filters.compute_filter [req-fbf96a04-09d5-4e3d-bf35-9b0935969296 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] (localhost.localdomain, localhost.localdomain) ram: 3258MB disk: 32768MB io_ops: 0 instances: 0 has not been heard from in a while
2021-09-18 11:01:09.141 2629 INFO nova.filters [req-fbf96a04-09d5-4e3d-bf35-9b0935969296 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filter ComputeFilter returned 0 hosts
2021-09-18 11:01:09.142 2629 INFO nova.filters [req-fbf96a04-09d5-4e3d-bf35-9b0935969296 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Filtering removed all hosts for the request with instance ID '2d43331b-bcff-43af-be11-5c3eefd8b7a5'. Filter results: ['AvailabilityZoneFilter: (start: 1, end: 1)', 'ComputeFilter: (start: 1, end: 0)']
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看nova-conductor.log日志：

```cpp
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager [req-fbf96a04-09d5-4e3d-bf35-9b0935969296 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Failed to schedule instances: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
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
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager Traceback (most recent call last):
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 1379, in schedule_and_build_instances
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     instance_uuids, return_alternates=True)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/conductor/manager.py", line 839, in _schedule_instances
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     return_alternates=return_alternates)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/client/query.py", line 42, in select_destinations
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     instance_uuids, return_objects, return_alternates)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/rpcapi.py", line 160, in select_destinations
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     return cctxt.call(ctxt, 'select_destinations', **msg_args)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/client.py", line 181, in call
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     transport_options=self.transport_options)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/transport.py", line 129, in _send
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     transport_options=transport_options)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 674, in send
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     transport_options=transport_options)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/_drivers/amqpdriver.py", line 664, in _send
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     raise result
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager Traceback (most recent call last):
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/oslo_messaging/rpc/server.py", line 235, in inner
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     return func(*args, **kwargs)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/manager.py", line 214, in select_destinations
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     allocation_request_version, return_alternates)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 96, in select_destinations
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     allocation_request_version, return_alternates)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 265, in _schedule
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     claimed_instance_uuids)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager   File "/usr/lib/python2.7/site-packages/nova/scheduler/filter_scheduler.py", line 302, in _ensure_sufficient_hosts
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager     raise exception.NoValidHost(reason=reason)
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager NoValidHost: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.216 2391 ERROR nova.conductor.manager 
2021-09-18 11:01:09.638 2391 WARNING nova.scheduler.utils [req-fbf96a04-09d5-4e3d-bf35-9b0935969296 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] Failed to compute_task_build_instances: 找不到有效主机，原因是 没有足够的主机可用。。
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
2021-09-18 11:01:09.646 2391 WARNING nova.scheduler.utils [req-fbf96a04-09d5-4e3d-bf35-9b0935969296 9942eb4321644336a0c7383d9f1c2bed a7d812868cb74f1a978035530f55f1d0 - default default] [instance: 2d43331b-bcff-43af-be11-5c3eefd8b7a5] Setting instance to ERROR state.: NoValidHost_Remote: \u627e\u4e0d\u5230\u6709\u6548\u4e3b\u673a\uff0c\u539f\u56e0\u662f \u6ca1\u6709\u8db3\u591f\u7684\u4e3b\u673a\u53ef\u7528\u3002\u3002
```


&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看nova-compute.log日志，没看出来有什么异常：

```cpp
2021-09-18 11:04:22.905 1556 INFO nova.virt.libvirt.imagecache [req-caca30f2-fddc-48ed-a0f9-d7250fb69311 - - - - -] image 853f71aa-07d5-49c4-beb5-81055ac6f722 at (/var/lib/nova/instances/_base/7e2bfe304c74a876f01642452d7930b251d7e3a8): checking
2021-09-18 11:04:22.914 1556 INFO nova.virt.libvirt.imagecache [req-caca30f2-fddc-48ed-a0f9-d7250fb69311 - - - - -] image f7cdcc6a-dbec-4d4a-82c4-4170a9cc1a19 at (/var/lib/nova/instances/_base/23a6b78eed18bc0b1ae4a43cde29aae49c9d666f): checking
2021-09-18 11:04:23.237 1556 INFO nova.virt.libvirt.imagecache [req-caca30f2-fddc-48ed-a0f9-d7250fb69311 - - - - -] Active base files: /var/lib/nova/instances/_base/7e2bfe304c74a876f01642452d7930b251d7e3a8 /var/lib/nova/instances/_base/23a6b78eed18bc0b1ae4a43cde29aae49c9d666f
```






