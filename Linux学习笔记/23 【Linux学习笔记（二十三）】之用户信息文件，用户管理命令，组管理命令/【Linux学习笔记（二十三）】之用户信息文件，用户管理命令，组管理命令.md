## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	用户文件
### （一）	/etc/passwd
&nbsp;  &nbsp;  &nbsp;  &nbsp;用户信息文件，保存着所有用户信息，每一行就是一个用户，包括能够登录的用户和无法登录的伪用户。

```cpp
root:x:0:0:root:/root:/bin/bash
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;第一列： 用户名

&nbsp;  &nbsp;  &nbsp;  &nbsp;第二列： 密码位，表示该用户有密码，但是真正的密码在/etc/shadow文件中，只有root用户能够查看。

&nbsp;  &nbsp;  &nbsp;  &nbsp;第三列： 用户ID

&nbsp;  &nbsp;  &nbsp;  &nbsp;0表示root用户，系统是通过UID来判断该用户是否是Root用户的，1-499表示伪用户ID，这些伪用户用来运行系统和服务的，每个服务都是有用户的，1-99是系统保留自动创建的，100-499是预留给用户创建系统账号的。500-60000是普通用户ID
&nbsp;  &nbsp;  &nbsp;  &nbsp;第四列：组ID，当添加用户时，如果不指定用户所属的组，则会建立和用户同名的组

&nbsp;  &nbsp;  &nbsp;  &nbsp;第五列：用户说明

&nbsp;  &nbsp;  &nbsp;  &nbsp;第六列：用户的家目录

&nbsp;  &nbsp;  &nbsp;  &nbsp;第七列：登录shell，可登录的用户都是/bin/bash,伪用户都是/sbin/nologin，表示不可登录，如果我们将自己的用户修改为/sbin/nologin，也无法登录了。
<br>


### （二）	/etc/shadow

&nbsp;  &nbsp;  &nbsp;  &nbsp;存储用户密码的文件，密码是经过加密的，伪用户的密码都是“!!”或者”*”,表示没有密码。我们可在root用户和普通用户的密码前面添加“”或者“*”，改变加密值让密码暂时失效，用户无法登录。

&nbsp;  &nbsp;  &nbsp;  &nbsp;第一列： 用户名

&nbsp;  &nbsp;  &nbsp;  &nbsp;第二列： 加密密码

&nbsp;  &nbsp;  &nbsp;  &nbsp;第三列： 密码最近更改时间，用天数的时间戳表示，从1970年1月1日开始

&nbsp;  &nbsp;  &nbsp;  &nbsp;第四列： 两次密码的修改间隔

&nbsp;  &nbsp;  &nbsp;  &nbsp;第五列： 密码有效期

&nbsp;  &nbsp;  &nbsp;  &nbsp;第六列： 密码到期前的警告天数

&nbsp;  &nbsp;  &nbsp;  &nbsp;第七列： 密码过期后的宽限天数

&nbsp;  &nbsp;  &nbsp;  &nbsp;第八列： 密码失效天数，到了用户就失效了

&nbsp;  &nbsp;  &nbsp;  &nbsp;第九列： 无
<br>



### （三）	/etc/group

&nbsp;  &nbsp;  &nbsp;  &nbsp;存储组的信息，每一行都是一个组。

```cpp
root:x:0:root
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;第一列：组名

&nbsp;  &nbsp;  &nbsp;  &nbsp;第二列：组密码，组一般只有root用户有权限，将组密码赋予一个用户后，该用户可以操作组。

&nbsp;  &nbsp;  &nbsp;  &nbsp;第三列： GID

&nbsp;  &nbsp;  &nbsp;  &nbsp;第四列： 此组中支持的其他用户，附加组是此组的用户

&nbsp;  &nbsp;  &nbsp;  &nbsp;初始组： 每个用户都有一个同名初始组

&nbsp;  &nbsp;  &nbsp;  &nbsp;附加组： 每个用户可以属于多个附加组，将用户加入组就是加入附加组，这里显示的就是该组的附加用户
<br>



### （四）	/etc/gshadow

&nbsp;  &nbsp;  &nbsp;  &nbsp;保存组密码的文件。
<br>



### （五）	用户模板目录
&nbsp;  &nbsp;  &nbsp;  &nbsp;/etc/skel/，这个目录中有一些隐藏文件，在创建新的用户时会添加到新用户的家目录下。如果我们要在新用户创建的家目录下自动生成一些文件，可以在模板目录下创建一些文件，它会自动添加到新用户的家目录下。

<br>



### （六）	用户邮箱目录
&nbsp;  &nbsp;  &nbsp;  &nbsp;在/var/spool/mail目录下，创建用户同名的邮箱目录。
<br>



### （七）	用户的家目录
&nbsp;  &nbsp;  &nbsp;  &nbsp;/home目录下用户同名目录。

<br>



## 二．	用户管理命令

### （一）	添加用户
&nbsp;  &nbsp;  &nbsp;  &nbsp;useradd命令，格式如下所示：

```cpp
useradd 选项 用户名
```

选项：
&nbsp;  &nbsp;  &nbsp;  &nbsp;-u ID：    指定UID，没有任何必要

&nbsp;  &nbsp;  &nbsp;  &nbsp;-g 组名： 指定初始组，没有必要，会默认创建

&nbsp;  &nbsp;  &nbsp;  &nbsp;-G 组名： 指定附加组

&nbsp;  &nbsp;  &nbsp;  &nbsp;-c 说明： 添加说明

&nbsp;  &nbsp;  &nbsp;  &nbsp;-d 目录： 指定家目录，没有必要

&nbsp;  &nbsp;  &nbsp;  &nbsp;-s shell：  /bin/bash或者 /sbin/nologin

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，每次我们添加了一个用户后，就要设置用passwd命令设置该用户的密码，不然该用户是无法登录的。

```cpp
useradd t1
passwd t1
```
<br>




### （二）	useradd默认值
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用useradd命令添加用户时有默认的参数配置，分别在/etc/default/useradd 和/etc/login.defs 两个文件中。
<br>



#### 1.	/etc/default/useradd
&nbsp;  &nbsp;  &nbsp;  &nbsp;该文件中的参数如下：

&nbsp;  &nbsp;  &nbsp;  &nbsp;HOME=/home 表示用户的家目录位置

&nbsp;  &nbsp;  &nbsp;  &nbsp;INACTIVE=-1  表示密码过期后的宽限天数

&nbsp;  &nbsp;  &nbsp;  &nbsp;EXPIRE=       表示密码的失效时间，用时间戳表示

&nbsp;  &nbsp;  &nbsp;  &nbsp;SHELL=/bin/bash 用户的默认shell，表示新创建的用户默认都具有shell赋予的权限

&nbsp;  &nbsp;  &nbsp;  &nbsp;SKEL=/etc/skel  定义用户的模板目录位置

&nbsp;  &nbsp;  &nbsp;  &nbsp;CREATE_MAIL_SPOOL=yes 默认创建邮箱
<br>



#### 2.	/etc/login.dfs

&nbsp;  &nbsp;  &nbsp;  &nbsp;MAIL_DIR /var/spool/mail   新用户的邮箱目录

&nbsp;  &nbsp;  &nbsp;  &nbsp;PASS_MAX_DAYS 99999    密码的有效期

&nbsp;  &nbsp;  &nbsp;  &nbsp;PASS_MIN_DAYS 0         两次密码的间隔时间

&nbsp;  &nbsp;  &nbsp;  &nbsp;PASS_MIN_LEN  5         密码最小长度

&nbsp;  &nbsp;  &nbsp;  &nbsp;PASS_WARN_AGE 7        密码到期前的警告天数

&nbsp;  &nbsp;  &nbsp;  &nbsp;CREATE_HOME yes         是否创建家目录

&nbsp;  &nbsp;  &nbsp;  &nbsp;UMASK 077                用户家目录的默认权限为700

&nbsp;  &nbsp;  &nbsp;  &nbsp;USERGROUPS_ENAB yes     删除用户时，是否删除用户初始组

&nbsp;  &nbsp;  &nbsp;  &nbsp;ENCRYPT_METHOD SHA512  密码加密方式，原先使用MD5

<br>



### （三）	设定密码

&nbsp;  &nbsp;  &nbsp;  &nbsp;passwd 命令，普通用户只能修改自己的密码，root用户能够修改所有人的密码。

root用户：

```cpp
passwd 选项 用户名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-l: 暂时锁定用户，即在/etc/shadow中将该用户的加密密码添加了！！，使密码失效而已。

&nbsp;  &nbsp;  &nbsp;  &nbsp;-u: 解锁用户

&nbsp;  &nbsp;  &nbsp;  &nbsp;--stdin: 通过管道符输出的数据作为用户的密码，主要在批量添加用户时使用，无需与系统交互，直接设置密码，但是这样的问题是所有用户的密码是一样的，我们可以通过修改shadow文件的密码修改日期字段（第三个字段）为0，这样用户在第一次登陆时就会强制修改密码了。

```cpp
echo “123” | passwd –stdin user1
```

普通用户：

```cpp
passwd
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改自己的密码
<br>



### （四）	修改用户信息

&nbsp;  &nbsp;  &nbsp;  &nbsp;usermod命令用于修改已经添加的用户信息，选项与useradd差不多，很多都是没有必要的，最常用的就是-G，修改用户的附加组，其他的就不介绍了。
<br>



### （五）	删除用户

&nbsp;  &nbsp;  &nbsp;  &nbsp;userdel命令用户删除用户，注意要同时删除家目录。

```cpp
userdel [-r] 用户名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-r: 删除家目录
<br>



### （六）	切换用户身份

&nbsp;  &nbsp;  &nbsp;  &nbsp;su 命令可以切换成不同的用户身份，如果不带“-”选项，环境变量env是不会切换的。

```cpp
su 选项 用户名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-: 表示连带用户的环境变量一起切换

&nbsp;  &nbsp;  &nbsp;  &nbsp;-c 命令： 仅执行一次命令，而不切换身份

&nbsp;  &nbsp;  &nbsp;  &nbsp;exit退出当前用户

<br>



## 三．	组管理命令

### （一）	添加用户组
&nbsp;  &nbsp;  &nbsp;  &nbsp;groupadd命令，格式如下：

```cpp
groupadd 选项 组名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-g GID : 指定组ID

<br>


### （二）	删除用户组
&nbsp;  &nbsp;  &nbsp;  &nbsp;groupdel命令，格式如下：

```cpp
groupdel 组名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，要删除一个组，必须保证该组中没有初始用户，才可以删除，如果组中有附加用户，可以删除。
<br>



### （三）	添加附加组
&nbsp;  &nbsp;  &nbsp;  &nbsp;gpasswd命令之前用于设定组密码并制定组管理员，不过很少用，现在用于为用户添加附加组。

```cpp
gpasswd 选项 组名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-a 用户名： 将用户加入组
&nbsp;  &nbsp;  &nbsp;  &nbsp;-d 用户名： 将用户从组中删除

&nbsp;  &nbsp;  &nbsp;  &nbsp;当然，这个操作useradd也能够完成，记住一个就行。






