## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	EXSI的安装
### （一）	ESXI介绍
 &nbsp;  &nbsp;  &nbsp;  &nbsp; VMware ESXI 是一款行业领先，专门构建的裸机Hypervisor，采用裸金属架构，完全虚拟化，直接安装在物理服务器上，作用是划分多个虚拟机。
<br>


### （二）	ESXI安装

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 在VMware上创建虚拟机，选择VMware ESX(X)，配置相应的参数，注意内存至少4个G，设置两块网卡，都选择主机模式，再从网上下载VMware VMvisor installer，CD/DVD选择这个文件，开启此虚拟机。

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 安装过程中遇到虚拟机按键与宿主机按键冲突的问题，因此在VMware的配置中添加了增强型虚拟按键。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 设置Root用户为wangdage，密码为wangdage。在ESXI6.5以前的版本中，使用C/S模式连接到ESXI中，6.5之后使用B/S模式连接，因此我们需要下载VMware-viclient，连接ESXI。这个客户端依赖于.Net4.0框架，未安装的需要提前安装。

 &nbsp;  &nbsp;  &nbsp;  &nbsp; ESXI安装完成后，重启虚拟机后按F2进入配置，首先需要输入用户名root和之前设置的密码。首先配置网络，选中之前添加的两块网卡，按空格键选中；然后配置IPV4，选择静态地址，设置静态地址为10.10.10.11， 掩码设置为255.255.255.0，网关设置为10.10.10.11。并且要设置VMware的虚拟网络编辑器，将主机模式的子网IP设置为10.10.10.0，和静态IP地址在一个网段中，这样宿主机Windows才能够Ping通虚拟机。

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 登录已经安装好的VMware viclient,使用账户密码登录到虚拟机中，说明安装过程全部完成。

<br>


## 二． EXSI测试

### （一） 添加用户
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 假设李四管理着Mysql集群，张三管理着LVS集群，现在我们要在EXSI中添加两个用户，分别为lisi，zhangsan，密码的设置非常严格，字母大小写，数字，特殊字符都得有，这里设置的是Wangdage123-=。
<br>



### （二） 设置角色
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 添加完用户后，为用户赋予权限，这里使用角色来为用户赋予权限，每个角色有固定的权限，只要该用户被赋予了某个角色，该用户就拥有了该角色的权限。角色可以使用默认的三种，也可以自己新建角色，选择权限。

<br>


### （三） 添加资源池
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 由于这两个角色管理着两个集群，因此我们需要给这个虚拟机添加两个资源池mysql和lvs，每个资源池赋予4个G的内存，并且允许扩展一些内存。
<br>



### （四） 为资源池添加用户和绑定的角色
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 为mysql资源池添加用户lisi，并且绑定对应的角色user，同理，lvs资源池也是一样。现在，就可以通过zhangsan和lisi用户登录ESXI了，不同用户登录的是不同的资源池。

<br>



## 三．	SAN 组网

### （一）	设置SAN组网
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 之前我们在EXSI中设置了两块网卡，EXSI是Hypervisor，直接部署在硬件之上，因此我们在虚拟机中查看网卡时会发现有两块物理网卡与虚拟机相连，通过类似交换机的方式。一个网卡采用全双工的方式连接着，另一个网卡处于待机状态，这是一块备用网卡，以便主网卡失效时顶替上去，可是网卡有很高的稳定性，一般是不会失效的，因此这块网卡闲置着非常浪费资源。

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 主网卡连接公网交换机，负责数据通讯；副网卡连接内网交换机，负责内网之间数据传输，如分布式存储。当前只有一个虚拟交换机连接着主网卡，因此，需要另外添加一个虚拟交换机专用于连接副网卡。

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 这样，当我们在主机上创建虚拟机时，设置两个虚拟网卡，一块连接着负责公网通信的主虚拟交换机，一块连接着负责内网数据传输的副虚拟交换机。这样，虚拟机的两条通信链路就打通了，一条是负责公网通信的，从虚拟网卡-虚拟交换机-物理网卡-公网交换机-公网路由器-客户端；一条是负责内网数据传输的，从虚拟网卡-虚拟交换机-物理网卡-内网交换机-其他物理网卡。这样就实现了SAN网络了。

<br>



### （二）	安装CentOS虚拟机

#### 1.	配置网卡
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 创建一个虚拟机，要实现SAN网络，需要两块网卡，注意设置网卡时，两块网卡的名称一定是不同的，不然无法实现NAS网络，适配器选择一样的。
<br>



#### 2.	安装操作系统

##### （1）上传镜像文件
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 由于我们在主机中使用远程连接EXSI，因此我们需要将镜像文件上传到服务器中，才能够安装，因此在服务器的配置中，查看存储器，右键浏览数据存储可以看到服务器中的数据，在这里就可以上传镜像文件了，不过速度比较慢。上传成功后，编辑虚拟机设置，选择服务器中的镜像文件作为CD/DVD驱动器，就可以安装系统了。
<br>



##### （2）连接本地主机的镜像文件、

 &nbsp;  &nbsp;  &nbsp;  &nbsp; 由于上传速度过慢，我们可以选择使用本地主机的镜像文件，首先要开启虚拟机，然后选择本地的镜像文件作为CD/DVD驱动器，然后按下CTRL+ALT+DELETE，重启虚拟机，就会进入到CentOS的安装界面了。

<br>



## 四．	EXSI的补充
### （一）	OVF模板
 &nbsp;  &nbsp;  &nbsp;  &nbsp; OVF模板是完整的虚拟机模板，当我们想要将一个已经安装好的虚拟机复制到另一个虚拟机上时，就可以通过导出OVF模板的方式，导入该模板到另一个虚拟机中。

<br>


### （二）	虚拟机选项
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 可查看不同虚拟机实时占用的资源情况。
<br>



### （三）	资源分配选项
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 可查看不同资源分配的情况，不是实时的。
<br>



### （四）	性能选项
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 性能选项中用折线图的形式显示了资源的实时占用情况。
<br>



### （五）	虚拟机自启动选项
 &nbsp;  &nbsp;  &nbsp;  &nbsp; 当服务器电源重启后，虚拟机需要自动地在服务器启动后开启，同时还要设置延迟时间，以免在服务器未启动的情况下虚拟机启动失败。



