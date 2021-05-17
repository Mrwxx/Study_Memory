## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


#### 一. 安装PythonDebug版本
&nbsp;  &nbsp;  &nbsp;  &nbsp;安装Python，如果已经安装Python，需要修改Python的安装设置，将Debug版本一起安装进来。
在卸载或更改程序，找到Python，右击更改，可以添加Debug版本。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200613160049602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
#### 二. 给VS OPENCV.sln 添加Python37_d.lib的路径
&nbsp;  &nbsp;  &nbsp;  &nbsp;Python37_d.lib在Python的安装路径的Libs文件夹中。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200613160423533.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
#### 三. 设置opencv_python3的属性，右击选择属性
1. 将python37_d.lib所在文件夹的路径添加到库目录里面
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200613160721532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
2. 然后在附加依赖项中添加python37_d.lib，就是刚才找不到的这个库文件的名字
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200613160836974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
#### 四. 右键重新生成opencv_python3，大功告成！！！

