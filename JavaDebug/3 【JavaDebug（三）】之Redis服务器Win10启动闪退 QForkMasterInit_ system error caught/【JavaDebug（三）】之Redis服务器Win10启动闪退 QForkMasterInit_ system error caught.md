## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. Bug描述
### （一）环境描述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Win10家庭版
&nbsp;  &nbsp;  &nbsp;  &nbsp;Redis 2.8.9
<br>


### （二）Bug描述
&nbsp;  &nbsp;  &nbsp;  &nbsp;首次在Win10上使用Redis时，执行redis-server.exe直接闪退，用cmd打开报如下的错误：

```cpp
QForkMasterInit: system error caught. error code=0x000005af, message=VirtualAllocEx failed.: unknow
```
<br>



## 二. 解决方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;因为没有设置Redis的最大内存，我们需要手动为Redis的配置文件redis.wiindows.conf添加内存设置：

```cpp
maxmemory 268435456
maxheap 314572800
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;在cmd中重新打开redis-server.exe文件，后面要加上redis.windows.conf配置文件。

```cpp
D:\JAVA\redis-2.8.9>redis-server.exe redis.windows.conf
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;成功登陆Redis服务器，问题成功解决。




