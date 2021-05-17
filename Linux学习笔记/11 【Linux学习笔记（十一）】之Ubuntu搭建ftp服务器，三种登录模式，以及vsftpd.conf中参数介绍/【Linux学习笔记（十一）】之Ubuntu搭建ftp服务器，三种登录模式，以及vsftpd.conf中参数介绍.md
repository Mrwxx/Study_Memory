## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Ubuntu搭建FTP服务器
### （一）	安装vsftpd
&nbsp;  &nbsp;  &nbsp;  &nbsp; vsftpd(Very Secure ftp deamon)，是一款运行在Linux操作系统上的ftp服务程序，具有很高的安全性，传输速度，以及支持虚拟用户验证的特点。

```cpp
sudo apt-get install vsftpd
```
<br>


### （二）	管理vsftpd状态

#### 1.	开启vsftpd服务

```cpp
service vsftpd start
```
<br>



#### 2.	关闭vsftpd服务

```cpp
service vsftpd stop
```
<br>



#### 3.	重启vsftpd服务

```cpp
service vsftpd restart
```
<br>



#### 4.	查看vsftpd服务状态

```cpp
service vsftpd status
```
<br>



### （三）	与vsftpd服务器有关的文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; vsftpd服务的配置文件默认在 /etc/vsftpd/conf

&nbsp;  &nbsp;  &nbsp;  &nbsp; vsftpd服务的根目录，即ftp服务器的主目录需要我们自己查找，我这里在/var/ftp中没有找到这个目录。在/etc/passwd文件中的 /ftp:项中，可以看到默认目录的路径，同时这是匿名用户的目录，当我们以匿名用户访问ftp时访问的就是这个目录，本机中显示的是/srv/ftp

<br>



## 二． 三种模式登录ftp
### （一） 匿名用户登录ftp
&nbsp;  &nbsp;  &nbsp;  &nbsp; 修改vsftpd的配置文件vsftpd.conf，将以下的配置添加进去：

```cpp
anonymous_enable=yes
anon_upload_enable=yes
anon_mkdir_write_enable=yes
anon_umask=022
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 保存退出后，重启vsftpd服务，现在我们就可以通过匿名用户登录ftp了。默认登录的目录是上面我们找出的默认路径 ： /srv/ftp。但是，目前我们无法通过ftp对该目录做操作，因为该目录的权限是root拥有的，因此，我们需要将该权限改为ftp账户，并且将文件夹的权限全部打开。
<br>



### （二）本地用户模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们可以使用本地用户登录ftp，这样更加地安全。首先要做的当然是修改vsftpd的配置文件了，将之前配置的匿名登录的注释掉，添加本地登录的配置：

```cpp
anonymous_enable=no
local_enable=yes
write_enable=yes
local_unmask=022
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 保存退出，重启vsftpd服务，再次尝试使用本地账户登录ftp，通过测试可以得知，使用非root账户可以登录，而root用户无法登录，因为在/etc/ftpusers文件中存在着root用户，这个文件存储着拒绝登录ftp的账户名称，因此，将root注释掉即可。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，使用本地用户登录进入的是该用户的家目录，即home下的用户目录，如我的账户是wmxx，那么访问的就是/home/wmxx目录，由于该目录的所有者和所属组都是用户自己，因此可以对该目录进行操作。
<br>



### （三）虚拟用户模式
&nbsp;  &nbsp;  &nbsp;  &nbsp; 虚拟用户模式是最安全的模式，它需要为ftp服务单独创建用户数据库文件，虚拟出用来进行口令验证的账户信息，而这些账户信息在服务器系统中实际上是不存在的，仅供ftp服务程序进行认证使用的，这样即使黑客破解了账户信息也没有办法登录。
<br>



#### 1.	创建ftp认证的用户数据库文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 首先创建list文件，其中奇数行为用户名，偶数行为密码。然后将明文的list文件通过db_load命令转换为数据库文件，降低数据库文件的权限，避免其他人看到该文件的内容，最后删掉原有的list文件

```cpp
vim vuser.list
```
vuser.list中的内容：
```cpp
zhangsan
redhat
lisi
redhat
```
	

```cpp
db_load -T -t hash -f vuser.list vuser.db
chmod 600 vuser.db
rm -f vuser.list
```
<br>



#### 2.	创建虚拟用户登录后访问的默认目录以及虚拟用户映射的系统本地用户
&nbsp;  &nbsp;  &nbsp;  &nbsp; 首先设置虚拟用户默认的访问目录，并且设置映射的系统本地用户，同时该本地用户不能登录ftp，以免影响虚拟用户的登录。

```cpp
useradd -d /var/ftproot -s /sbin/nologin virtual
chmod -Rf 755 /var/ftproot
```
<br>



#### 3.	创建用于支持虚拟机用户的PAM文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; PAM（可插拔认证模块）是一种认证机制，系统管理员可以用来轻易地调整服务程序的认证方式，而不必对应用程序进行任何修改。新建一个用于虚拟用户认证的PAM文件vsftpd.vu，其中文件内的”db=”参数之前生成的用户数据库文件的路径，但不用写文件的后缀。

```cpp
vim /etc/pam.d/vsftpd.vu
```
vsftpd.vu中的内容：
```cpp
auth required pam_userdb.so db=/etc/vsftpd/vuser
account required pam_userdb.so db=/etc/vsftpd/vuser
```
<br>



#### 4.	配置vsftpd的主配置文件vsftpd.conf
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在vsftpd程序的主配置文件vsftpd.conf中通过pam_service_name参数将PAM认证文件的名称修改为vsfptd.vu，同时设置虚拟用户登录的其他设置：

```cpp
anonymous_enable=no
local_enable=yes
guest_enable=yes
guest_username=virtual
pam_service_name=vsftpd.vu
allow_writeable_chroot=yes
```
<br>



#### 5.	为虚拟用户设置不同的权限
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们在用户数据库文件中添加了两个虚拟用户zhangsan和lisi，可以分别设置不同的权限，只需要新建一个目录，在里面分别创建两个以zhangsan和lisi命名的文件，写入允许的权限，按照匿名用户的参数形式写入：

```cpp
vim zhangsan
```
zhangsan文件中的内容：
```cpp
anon_upload_enable=yes
anon_mkdir_write_enable=yes
```
<br>



#### 6.	再次修改vsftpd.conf添加虚拟用户权限文件
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过user_config_dir参数设置虚拟用户权限的文件夹路径即可。

```cpp
user_config_dir=/etc/vsftpd/vusers_dir
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 至此，虚拟用户就可以直接登录了，进入的默认目录就是我们设置的默认目录/var/ftproot。
<br>




## 三．	 vsftpd.conf中参数介绍
### （一）	匿名用户模式

```cpp
anonymous_enable=YES　　 #是否启用匿名用户
no_anon_password=YES 　　#匿名用户login时不询问口令
anon_upload_enable=（yes/no)；  #控制匿名用户对文件（非目录）上传权限。
anon_world_readable_only=（yes/no）； #控制匿名用户对文件的下载权限
anon_mkdir_write_enable=（yes/no）； #控制匿名用户对文件夹的创建权限
anon_other_write_enable=（yes/no）； #控制匿名用户对文件和文件夹的删除和重命名
write_enable=YES #全局设置，是否容许写入（无论是匿名用户还是本地用户，若要启用上传权限的话，就要开启他）
anon_root=(none) #匿名用户主目录
anon_max_rate＝（0） #匿名用户速度限制
anon_umask＝（077） #匿名用户上传文件时有掩码(若想让匿名用户上传的文件能直接被匿名下载，就这设置这里为073)
chown_uploads=YES #所有匿名上传的文件的所属用户将会被更改成chown_username
chown_username=whoever #匿名上传文件所属用户名
```
<br>



### （二）	本地用户模式

```cpp
write_enable=YES #可以上传(全局控制) 删除，重命名
local_umask=022  #本地用户上传文件的umask
userlist_enable=YES #限制了这里的用户不能访问
local_root  #设置一个本地用户登录后进入到的目录
user_config_dir  #设置用户的单独配置文件，用哪个帐户登陆就用哪个帐户命名
download_enable  #限制用户的下载权限
chown_uploads=YES #所有匿名上传的文件的所属用户将会被更改成chown_username
chown_username=whoever #匿名上传文件所属用户名
chroot_list_enable=YES　#如果启动这项功能，则所有列在chroot_list_file之中的使用者不能更改根目录
chroot_list_file=/etc/vsftpd/chroot_list #指定限制的用户文件
user_config_dir= #后面跟存放配置文件的目录。用来实现不同用户不同权限。
```
<br>



### （三）	虚拟用户模式

```cpp
pam_service_name=vsftpd  #设置PAM使用的名称，默认值为/etc/pam.d/vsftpd。
check_shell=YES   #（注意：仅在没有pam验证版本时有用,是否检查用户有一个有效的shell来登录 )
guest_enable= YES/NO  #启用虚拟用户。默认值为NO。
guest_username=ftp  #这里用来映射虚拟用户。默认值为ftp。
virtual_use_local_privs=YES/NO #当该参数激活（YES）时，虚拟用户使用与本地用户相同的权限。
    #当此参数关闭（NO）时，虚拟用户使用与匿名用户相同的权限。默认情况下此参数是关闭的（NO）。
```






