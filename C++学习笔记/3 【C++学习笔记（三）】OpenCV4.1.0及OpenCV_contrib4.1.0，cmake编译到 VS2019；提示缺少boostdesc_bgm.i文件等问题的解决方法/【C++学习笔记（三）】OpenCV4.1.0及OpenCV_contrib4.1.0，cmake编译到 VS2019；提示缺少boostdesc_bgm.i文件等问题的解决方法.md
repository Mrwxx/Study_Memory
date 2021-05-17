## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

#### 一. 错误报告的原因：
&nbsp;  &nbsp;  &nbsp;  &nbsp;Opencv_contrib 需要自己编译，我们选用的是**cmake**进行编译
&nbsp;  &nbsp;  &nbsp;  &nbsp;在cmake中设置好路径，进行configure，标红了许多，提示有warning.这也是后面在VS 中install报错的原因，缺少相关的文件。cmake是直接从源码下载相关的文件到本地，当文件的地址无法访问时，相关的文件便没有下载到本地，而是生成了一个**.cache**文件夹。该文件夹如图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200613142703108.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
#### 二. 未下载的文件位置
&nbsp;  &nbsp;  &nbsp;  &nbsp;该文件夹中生成的都是**未下载成功**的文件，需要我们手动来下载。下载的地址在哪呢？这时我们就要查找cmake的**日志**了，该日志的地址在我们新编译存储的文件夹中，我设置的文件夹名称为Newbuild,日志的名字为CMakeDownloadLog.txt，如图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200613143022880.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
&nbsp;  &nbsp;  &nbsp;  &nbsp;打开日志，截取一段看看

#cmake_download "D:/opencv/opencv346/sources/.cache/ffmpeg/41b81bb9a50cabd4bea385f7b50a069a-opencv_ffmpeg.dll" 
"https://raw.githubusercontent.com/opencv/opencv_3rdparty/fe71c0ad807fdc33c2178e48e488f1e9b177c39a/ffmpeg/opencv_ffmpeg.dll"

&nbsp;  &nbsp;  &nbsp;  &nbsp;**日志的意思是：**cmake尝试下载opencv_ffmpeg.dll文件,**md5**fe71c0ad807fdc33c2178e48e488f1e9b177c39a，**下载地址**为https://raw.githubusercontent.com/opencv/opencv_3rdparty/fe71c0ad807fdc33c2178e48e488f1e9b177c39a/ffmpeg/opencv_ffmpeg.dll 
&nbsp;  &nbsp;  &nbsp;  &nbsp;但下载失败，因此报错。所以我们手动下载该文件并将该**文件名改为md5-opencv_ffmpeg.dll**，并把该文件放到.cache/ffmpeg文件夹下。
#### 三. 下载完所有文件后
&nbsp;  &nbsp;  &nbsp;  &nbsp;将所有未下载的文件用同样的方法下载好后，重新configure,配置路径，**注意，这里需要勾选BUILD_opencv_world,以及OPENCV_ENABLE_NONFREE**，然后generate,打开VS,根据自己的需要，设置好Debug,x64;或者是Release, x64后，点击生成->生成解决方案.经过漫长等待后，在解决方案资源管理器中，打开CmakeTragets,右击INSTALL,选择仅用于项目，仅生成INSTALL。
&nbsp;  &nbsp;  &nbsp;  &nbsp;打开新的VS项目，为项目添加包含目录，库目录，链接库，这都是老生常谈了，不再赘述。至此，如果没有出现错误的话，顺利结束你的编译之旅。
&nbsp;  &nbsp;  &nbsp;  &nbsp;Errrrrr..........................
#### 怎么可能这么容易结束呢？你就没有报错吗？你这个可爱的孩子？
#### 四. 可能出现的错误：
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.在文件的下载过程中，有的文件是可以直接下载的，有的是直接打开了网页，如https://raw.githubusercontent.com/opencv/opencv_3rdparty/fe71c0ad807fdc33c2178e48e488f1e9b177c39a/ffmpeg/ffmpeg_version.cmake ，这时我们需要点击右键另存为txt文件，再修改后缀为cmake文件。
&nbsp;  &nbsp;  &nbsp;  &nbsp;2.文件的下载也有另外一种方式，直接到github的opencv_contrib页面进行下载，选择相应版本的opencv_contrib，下载相应的模块文件如ffmpeg等等
&nbsp;  &nbsp;  &nbsp;  &nbsp;3.LNK1104 无法打开文件“python37_d.lib”。说明你的Python在安装时没有安装Debug版本，那么重新修改Python的安装设置，将Debug版本添加进来。我的另一篇博客有介绍：[Python37_d.lib
](https://blog.csdn.net/Mrwxxxx/article/details/106733633)
&nbsp;  &nbsp;  &nbsp;  &nbsp;4.最重要的BUG，如果编译OpenCV，那么你就要检查C盘的Windows文件夹下的syswow64和system32文件夹中是否含有opencv相关的dll，如果有，将他们全部删除，不然在运行程序时就会报错：无法定位程序输入点于动态链接库错误；

&nbsp;  &nbsp;  &nbsp;  &nbsp;历经许多磨难，最终程序运行成功！仅以此博客记录这漫长的过程！！！




