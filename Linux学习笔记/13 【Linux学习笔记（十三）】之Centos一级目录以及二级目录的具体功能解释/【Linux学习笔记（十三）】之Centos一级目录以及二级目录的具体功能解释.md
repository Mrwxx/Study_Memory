## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Linux一级目录
### （一）	/bin/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 存放系统命令的目录。普通用户和超级用户都可以执行，是/usr/bin/目录的软链接。
<br>


### （二）	/sbin/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 存放系统命令的目录。只有超级用户可以执行，是/usr/sbin/目录的软链接。
<br>



### （三）	/usr/bin/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 存放系统命令的目录，普通用户和超级用户都可以执行。
<br>



### （四）	/usr/sbin/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 存放系统命令的目录，只有超级用户才可以执行。
<br>



### （五）	/boot/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统启动目录，保存与系统启动相关的文件，如内核文件和启动引导程序（grub）。
<br>



### （六）	/dev/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 设备文件保存位置。
<br>



### （七）	/etc/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置文件保存位置，系统中所有采用默认安装方式（rpm安装）的服务配置文件全部保存在此目录中，如用户信息，服务的启动脚本。
<br>



### （八）	/home/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 普通用户的家目录，在创建用户时，每个用户有一个默认登录和保存自己数据的位置，在/home/下建立一个和用户名相同的目录，如用户wxx的家目录就是/home/wxx
<br>



### （九）	/lib/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统调用的函数库保存位置，是/usr/lib/的软链接。
<br>



### （十）	/lib64/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 64位函数库的保存位置，是/usr/lib64的软链接。只要是以so.4.3这样.so+数字的格式的都是重要的系统库函数，不要随意删除。
<br>



### （十一） /list+found/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当系统意外崩溃或者机器意外关机时，会产生一些文件碎片。当系统启动时，fsck工具会检查这里，并且修复已经损坏的文件系统。这个目录只在每个分区中出现，为每个分区点的备份恢复目录。
<br>



### （十二） /media/, /misc/, /mnt/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 都是挂载目录，/media/系统建议用来挂载媒体设备，如软盘和光盘。/misc/建议用来挂载NFS服务的共享目录。/mnt/系统建议用来挂载额外的设备，如U盘，移动硬盘。
<br>



### （十三） /opt/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 第三方安装的软件保存位置，不过习惯上是将第三方软件安装到/usr/local/目录下面。

<br>


### （十四） /proc/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 虚拟文件系统，该目录中的数据并不保存在硬盘上，而是保存在内存上。主要保存系统的内核，进程，外部设备状态和网络状态。如/proc/cpuinfo是保存CPU信息的，/proc/devices保存设备驱动列表的，/proc/filesystems保存文件系统列表的。
<br>



### （十五） /sys/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 虚拟文件系统，数据都保存在内存中，主要是内核信息。
<br>



### （十六） /root/
&nbsp;  &nbsp;  &nbsp;  &nbsp; root用户的宿主目录。
<br>



### （十七） /run/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统运行时产生的数据，如ssid, pid等数据。
<br>



### （十八） /srv/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 服务数据目录，系统服务启动之后，在该目录下保存数据。
<br>



### （十九） /tmp/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统存放临时文件的目录，建议不要保存重要数据，建议开机清空。。

<br>


### （二十） /usr/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统软件资源目录，usr不是user的缩写，而是 UNIX Software Resource的缩写，存放的是系统软件资源。系统中的软件大部分在这个目录里面。


<br>


## 二．	Linux二级目录
### （一）	/usr/lib/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 应用程序调用的函数库保存位置。

<br>


### （二）	/usr/local/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 自己安装的软件保存位置，即程序的安装位置。

<br>


### （三）	/usr/share/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 应用程序的资源文件保存位置，如帮助文档，说明文档。
<br>



### （四）	/usr/src/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 源码包保存的位置， 建议仅仅保存内核相关源码，将自己下载的源码包保存到/usr/local/src/目录中，免得与内核源码弄混了、
<br>



### （五）	/usr/src/kernels/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 内核源码保存位置。
<br>



### （六）	/var/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 动态数据的保存位置，如缓存，日志以及软件运行过程中产生的文件。
<br>



### （七）	/var/www/html/
&nbsp;  &nbsp;  &nbsp;  &nbsp; RPM包安装的Apache的网页主目录。

<br>


### （八）	/var/lib/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 程序运行中需要调用的数据保存位置，如MySQL的数据库保存在/var/lib/mysql/目录中。

<br>


### （九）	/var/log/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统日志保存位置。
<br>



### （十）	/var/run/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 一些服务和程序运行后，它们的PID保存位置。
<br>



### （十一） /var/spool/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 放置队列数据的目录，即排队等待其他程序使用的数据，如邮件队列和打印队列。

<br>


### （十二） /var/spool/mail/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统新收到的邮件会保存在该目录中。
<br>



### （十三） /var/spool/cron/
&nbsp;  &nbsp;  &nbsp;  &nbsp; 系统的定时计划任务会保存在这个目录下面。


