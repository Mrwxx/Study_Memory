## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	在IDEA中使用Git
### （一）	在IDEA中配置Git
 &nbsp;  &nbsp;  &nbsp;IDEA能够自动检测到Git的安装位置，如果没有检测到，需要我们手动进行配置。选择File -> Settings打开设置窗口，找到Version Control下的Git选项。选择Git的安装目录后可以点击 “Test”测试是否配置正确。
<br>


### （二）	在IDEA中使用Git

#### 1.	在项目文件夹创建本地仓库
 &nbsp;  &nbsp;  &nbsp;首先，我们要在项目文件夹下创建本地仓库，之前我们是直接在该项目的目录下执行Git命令，则IDEA中我们选择VCS -> Import into Version Control -> Create Git Repository，创建本地仓库。 
<br>



#### 2.	将文件加入暂存区并提交
  &nbsp;  &nbsp;  &nbsp;将文件加入暂存区就是让Git来管理，但是有些文件是不需要让Git来管理的，如一些Maven自动生成管理的文件，如.idea文件夹，target文件夹，*.iml文件，这些都可以放到.gitignore文件中，Git就不会管理这些文件。设置好.gitignore文件后，我们可以点击项目的右键，选择Git -> Add，添加项目到暂存区中。添加到暂存区后，就可以提交到本地仓库中。

如，.gitignore文件的内容如下：

```cpp
.idea/
gittest.iml
target/
```
<br>



#### 3.	推送到远程仓库
 &nbsp;  &nbsp;  &nbsp;在该项目处点击右键，选择Git -> Repository -> Push，设置远程仓库的地址，点击push推送代码。
<br>



#### 4.	从远程仓库克隆
 &nbsp;  &nbsp;  &nbsp;在IDEA初始界面，选择Check out from Version Control的Git选项，设置远程仓库的地址即可。
<br>



#### 5.	从远程仓库中拉取代码
 &nbsp;  &nbsp;  &nbsp;在该项目处点击右键，选择Git -> Repository -> Pull，设置远程仓库的地址，点击Pull拉取代码。
<br>



#### 6.	版本对比
 &nbsp;  &nbsp;  &nbsp;在代码处点击右键选择 Git -> Compare With即可对比不同版本代码的不同。

<br>



## 二．	使用SSH协议传输数据
### （一）	Git支持的传输协议
 &nbsp;  &nbsp;  &nbsp;在使用远程仓库时，会涉及到数据的网络传输，Git支持多种数据传输协议：
 
 &nbsp;  &nbsp;  &nbsp;1.	本地协议（Local）
 
 &nbsp;  &nbsp;  &nbsp;2.	HTTPS协议
 
 &nbsp;  &nbsp;  &nbsp;3.	SSH（Secure Shell）协议
 
 &nbsp;  &nbsp;  &nbsp;4.	Git协议
<br>



### （二）	 SSH协议介绍
 &nbsp;  &nbsp;  &nbsp;SSH全称为Secure Shell（安全外壳协议）的缩写，是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议，利用SSH协议可以有效地防止远程管理过程中的信息泄露问题。

 &nbsp;  &nbsp;  &nbsp;由于本地Git仓库和远程仓库之间的传输是通过SSH加密的，所以必须让远程仓库服务器认证你的SSH Key，首先，我们要生成SSH Key。所谓的SSH Key就是密钥，我们必须为自己创建一对密钥（公钥和私钥），并把公钥放在需要访问的服务器上。 
<br>



### （三）	配置SSH协议
 &nbsp;  &nbsp;  &nbsp;使用Git提供的命令行工具Git Bash生成公钥和私钥，操作过程如下：

 &nbsp;  &nbsp;  &nbsp;1.	使用命令 ssh-keygen-t rsa 生成公钥和私钥，执行完成后再window本地用户.ssh目录下：
 
 `C:\Users\用户名\.ssh`下面生成公钥和私钥。公钥后缀为pub，如下所示：

```cpp
私钥： id_rsa
公钥： id_rsa_pub
```

 &nbsp;  &nbsp;  &nbsp;打开两个文件可以看到，都是一些字符串。
<br>



 &nbsp;  &nbsp;  &nbsp;2.	复制公钥文件字符串到码云服务器中， 在安全设置的SSH公钥设置中，粘贴即可，这样就完成了SSH的配置工作，现在就可以通过SSH的通信方式进行传输数据了。





