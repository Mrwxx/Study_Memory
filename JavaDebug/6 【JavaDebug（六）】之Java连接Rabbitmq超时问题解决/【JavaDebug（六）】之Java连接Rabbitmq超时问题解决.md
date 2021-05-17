## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	环境描述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Rabbitmq搭建在Ubuntu16.04
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;Win10中使用Java连接Rabbitmq
<br>


## 二．	问题描述
### （一）	问题概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在Java中连接Rabbitmq，出现连接超时异常，说明连接到虚拟机的Rabbitmq服务器异常，检查了主机，端口，虚拟机名称，用户名，密码后没有异常，那么连接应该是没有问题的，为何还有连接超时的问题呢？
<br>



### （二）	问题解决
 &nbsp;  &nbsp;  &nbsp;  &nbsp;这得从我安装Ubuntu时候说起了，当时安装完Ubuntu后，我就修改了主机名称，我将ubuntu改为了ubuntu_wxx，然而修改后的主机名称并没有在linux系统的hosts文件中，因此解析的时候，没有办法直接从该hosts文件中获取主机名称，需要多重解析，才能够获取到该主机名称，这就出现了连接超时的问题。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;因此，在hosts文件中添加新的主机名称映射：

```cpp
127.0.0.1 ubuntu_wxx
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;保存退出，重启rabbitmq-server服务，连接正常，一气呵成，艹艹艹！！！



