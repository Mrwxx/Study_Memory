## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	RDB持久化
### （一）	概述

&nbsp;  &nbsp;  &nbsp;  &nbsp;服务器的数据库状态表示着服务器中的非空数据库以及它们的键值对，因为Redis是内存数据库，它将自己的数据库状态存储在内存中，为了保持数据的持久化，Redis提供了RDB持久化功能，将内存中的数据库状态保存到磁盘中。RDB持久化既可以手动执行，也可以定期执行，生成的RDB文件是一个经过压缩的二进制文件，通过该文件可以还原成生成RDB文件时的数据库状态。
<br>


### （二）	RDB文件的创建和载入

#### 1.	 创建RDB文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;两个命令用于生成RDB文件，一个是SAVE, 一个是BGSAVE。SAVE命令会阻塞redis服务器进程，直到RDB文件创建完毕；而BGSAVE命令会派生出一个子进程，然后由子进程负责创建RDB文件，服务器进程继续处理命令请求。

&nbsp;  &nbsp;  &nbsp;  &nbsp;创建RDB文件实际是通过调用rdbSave函数完成的，在BGSAVE命令执行期间，客户端发送的SAVE命令会被拒绝，这两个命令无法同时执行是为了避免同时执行两个rdbSave函数调用。
<br>


#### 2.	载入RDB文件

&nbsp;  &nbsp;  &nbsp;  &nbsp;RDB文件的载入工作是在服务器启动时自动执行的，只要启动时检测到RDB文件存在就会载入。由于AOF文件的更新频率更高，如果服务器开启了AOF持久化功能，则优先使用AOF文件来还原数据库状态。
<br>


### （三）	自动间隔性保存

#### 1.	保存条件
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于BGSAVE命令可以在不阻塞服务器进程的情况下执行，所以Redis允许用户通过设置服务器配置的save选项，让服务器每隔一段时间自动执行一次BGSAVE命令。用户可以通过save选项设置多个保存条件，只要任意一个被满足，就会执行BGSAVE命令。

&nbsp;  &nbsp;  &nbsp;  &nbsp;我们可以通过制定配置文件或者传入启动参数的方式设置save选项，在redisServer服务器结构中，有一个saveparam数组，每个元素都是一个save选项的保存条件。

<br>

#### 2.	dirty计数器和lastsave属性

&nbsp;  &nbsp;  &nbsp;  &nbsp;服务器状态还维持着一个dirty计数器，记录着距离上一次成功执行SAVE命令或者BGSAVE命令后，服务器对数据库状态修改了所少次。lastsave属性是一个UNIX时间戳，记录了服务器上一次成功执行SAVE命令或者BGSAVE命令的时间。
<br>


### （四）	RDB文件结构

&nbsp;  &nbsp;  &nbsp;  &nbsp;RDB文件的开头是REDIS部分，用于载入文件时识别这是RDB文件；

&nbsp;  &nbsp;  &nbsp;  &nbsp;db_version表示RDB文件的版本号，即RDB结构的版本号；

&nbsp;  &nbsp;  &nbsp;  &nbsp;databases包含了所有的数据库以及各个数据库中的键值对数据；

&nbsp;  &nbsp;  &nbsp;  &nbsp;EOF常量标志着RDB文件正文内容的结束；

&nbsp;  &nbsp;  &nbsp;  &nbsp;check_sum保存着一个校验和，通过对前几部分的值进行计算得出的，在载入文件时会再次计算与该值对比，检查RDB文件是否出错或者损坏；

<br>

### （五）	分析RDB文件

&nbsp;  &nbsp;  &nbsp;  &nbsp;使用od命令来分析RDB文件，该命令可以用给定的格式转存（dump）并打印输出文件，如给定-c参数可以以ASCII编码的方式打印输入文件，-x参数以十六进制的方式打印输入文件。也可以使用Redis自带的RDB文件检查工具redis-check-dump。

