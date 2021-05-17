## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. Bug描述
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用IDEA创建一个Maven项目，在运行的时候报出了Bug: java不支持发行版本5。第一反应是运行环境的问题，可能是项目的编译环境和Maven的版本不匹配什么的。
<br>


## 二. 问题解决
### （一）修改project structure

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看项目的 project structure，点击project下的project查看项目的运行环境配置情况，修改SDK和JDK为合适的版本。

&nbsp;  &nbsp;  &nbsp;  &nbsp;点击project下的module查看模块的运行环境情况，这里显示的SDK果然是5.0版本，原来错误出现在这里，我们只要将它修改为合适的高版本即可。
<br>



### （二）修改IDEA的settings
&nbsp;  &nbsp;  &nbsp;  &nbsp;点击 Settings , 选择 “Build, Execution, Deployent”，点击其中的Java Compiler，设置 Target bytecode version 为本地的JDK版本，同样把 Project bytecode version设置为本地的JDK版本即可。

&nbsp;  &nbsp;  &nbsp;  &nbsp;以上的两步都做完后，错误就解决了。

