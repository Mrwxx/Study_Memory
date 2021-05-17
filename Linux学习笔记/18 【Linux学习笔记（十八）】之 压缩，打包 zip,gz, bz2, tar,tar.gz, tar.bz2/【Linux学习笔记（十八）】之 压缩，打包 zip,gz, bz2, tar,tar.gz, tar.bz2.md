## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	压缩与解压缩命令
### （一）	.zip
&nbsp;  &nbsp;  &nbsp;  &nbsp; .zip是Windows中最常用的压缩格式，Linux可以和Windows通用压缩文件。
<br>


#### 1.	zip
&nbsp;  &nbsp;  &nbsp;  &nbsp; 压缩命令是zip，在/usr/bin/zip中，用于压缩文件或目录。命令的格式如下：

```cpp
zip [选项] 压缩包名 源文件或源目录
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -r 压缩目录

```cpp
zip ana.zip anaconda-ks.cfg
```
<br>



#### 2.	unzip
&nbsp;  &nbsp;  &nbsp;  &nbsp; 解压缩命令，在/usr/bin/unzip中，命令格式如下：

```cpp
unzip [选项] 压缩包名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -d 指定解压缩位置，不指定位置的话就是在当前目录下

```cpp
unzip -d /tmp ana.zip
```
<br>



### （二）	.gz

#### 1.	gz
&nbsp;  &nbsp;  &nbsp;  &nbsp; Linux中最常用的压缩格式，在/bin/gzip中，能够压缩文件或目录，但是它不会打包，不能将多个文件或者目录压缩成一个。命令格式如下：

```cpp
gzip [选项] 源文件
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -c: 将压缩数据输出到标准输出中，可以用于保留源文件

&nbsp;  &nbsp;  &nbsp;  &nbsp; -d: 解压缩

&nbsp;  &nbsp;  &nbsp;  &nbsp; -r: 压缩目录

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于gz压缩文件后，源文件就会消失，因此使用-c选项，将压缩数据重定向到压缩文件中，这样源文件就不会被删除了：

```cpp
gzip -c anaconda.cfg > anaconda.cfg.gz
```
<br>



#### 2.	gunzip
&nbsp;  &nbsp;  &nbsp;  &nbsp; 解压缩命令，在/bin/gunzip中，只能解压缩到当前目录：

```cpp
gunzip anaconda.cfg.gz
```
<br>



### （三）	.bz2
#### 1.	bzip2
&nbsp;  &nbsp;  &nbsp;  &nbsp; bz2压缩比更好，gz压缩时间更快，但是bz2不能压缩目录。bzip2压缩命令再/usr/bin/bzip2中，命令格式如下：

```cpp
bzip2 [选项] 源文件
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -d : 解压缩

&nbsp;  &nbsp;  &nbsp;  &nbsp; -k:  压缩时保留源文件

&nbsp;  &nbsp;  &nbsp;  &nbsp; -v:  显示压缩的详细信息

```cpp
bzip2 anaconda.cfg
bzip2 -k anaconda.cfg
```

<br>


#### 2.	bunzip2
&nbsp;  &nbsp;  &nbsp;  &nbsp; 解压缩命令，在/usr/bin/bunzip2中，也是直接解压到当前目录：

```cpp
bunzip2 anaconda.cfg.bz2
```

<br>



## 二．	tar命令

### （一）	.打包
&nbsp;  &nbsp;  &nbsp;  &nbsp; tar命令只会打包不会压缩，即将多个文件打包为一个文件。在/bin/tar中，命令如下所示：

```cpp
tar [选项] [-f 压缩包名] 源文件或目录
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -c: 打包

&nbsp;  &nbsp;  &nbsp;  &nbsp; -f: 指定压缩包的文件名

&nbsp;  &nbsp;  &nbsp;  &nbsp; -v: 显示打包文件过程

```cpp
tar -cvf anaconda.cfg.tar anaconda.cfg
```
<br>



### （二）	解打包
&nbsp;  &nbsp;  &nbsp;  &nbsp; 同样使用tar命令，只是改变了选项：

&nbsp;  &nbsp;  &nbsp;  &nbsp; -x: 解打包

&nbsp;  &nbsp;  &nbsp;  &nbsp; -f: 指定包的文件名

&nbsp;  &nbsp;  &nbsp;  &nbsp; -v: 显示解打包文件过程

&nbsp;  &nbsp;  &nbsp;  &nbsp; -t: 测试，只是查看包中的文件有哪些，并不会解打包

&nbsp;  &nbsp;  &nbsp;  &nbsp; -C: 指定解打包位置

```cpp
tar -xvf anaconda.cfg.tar
tar -xvf anaconda.cfg.tar -C /tmp
```
<br>



## 三．	tar.gz 和 tar.bz2

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通常情况下，我们会先打包，再压缩。即先调用tar命令，再调用gz或者bzip2命令，这样非常繁琐。于是，还是使用tar命令，但是有了直接打包压缩的命令选项：

&nbsp;  &nbsp;  &nbsp;  &nbsp; -z: 打包压缩为 tar.gz格式

&nbsp;  &nbsp;  &nbsp;  &nbsp; -j: 打包压缩为 tar.bz2格式

打包压缩与解压缩

```cpp
tar -zcvf tmp.tar.gz /tmp
tar -zxvf tmp.tar.gz 
```

只查看，不解压

```cpp
tar -ztvf test.tar.gz 
```

解压缩到指定位置

```cpp
tart -zxvf test.tar.gz -C /tmp
```

查看压缩文件中的内容后，指定解压缩特定文件到指定位置

```cpp
tar -zxvf test.tar.gz -C /tmp test/code
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，这个指定目录的选项只能放在其他选项之后！！！







