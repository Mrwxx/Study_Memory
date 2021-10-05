## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Git
### （一）	Git与SVN的对比
#### 1.	SVN
&nbsp;  &nbsp;  &nbsp;  &nbsp;SVN是集中式版本控制系统，版本库是集中放在中央服务器上的，开发人员只能从中央服务器中下载最新的版本进行开发，开发完毕后，需要将自己开发的代码提交到中央服务器中。

集中式版本控制工具缺点：

&nbsp;  &nbsp;  &nbsp;  &nbsp;服务器单点故障：当服务器出现故障时，所有的开发需要等待服务器的故障排除后才能继续进行。容错性很差
<br>


#### 2.	Git
&nbsp;  &nbsp;  &nbsp;  &nbsp;Git是分布式版本控制系统（Distributed Version Control System，DVCS），分为两种类型的仓库：本地仓库和远程仓库。本地仓库是开发人员自己电脑上的Git仓库，远程仓库是远程服务器上的Git仓库。

&nbsp;  &nbsp;  &nbsp;  &nbsp;当开发人员开发完毕后，可以通过Commit命名将代码提交到本地仓库上；若想将代码提交到远程仓库中，使用Push推送命令推送到远程仓库中；若想从远程仓库中下载代码到本地仓库，使用Pull拉取命令；若想直接将远程仓库复制到本地，就使用Clone克隆命令。
<br>



### （二）	Git工作流程
Git的工作流程如下所示：

&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	从远程仓库中通过Clone克隆代码到本地仓库中。

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	开发人员从本地仓库中通过checkout获取代码后进行代码修改。

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	开发人员修改完代码后，想要将代码提交到本地仓库中，首先将代码Add提交到暂存区，之后暂存区会将代码Commit到本地仓库中。

&nbsp;  &nbsp;  &nbsp;  &nbsp;4.	提交到本地仓库后，本地仓库保存着修改的历史版本。

&nbsp;  &nbsp;  &nbsp;  &nbsp;5.	当需要将代码上传到远程仓库时，可以通过Push从本地仓库推送代码到远程仓库中。

&nbsp;  &nbsp;  &nbsp;  &nbsp;6.	若想直接从远程仓库中获取代码，则可以通过Pull拉取代码到工作区中，不经过本地仓库。
<br>




## 二．	Git代码托管服务
### （一）	常用的Git代码托管服务
&nbsp;  &nbsp;  &nbsp;  &nbsp;通常我们是借助互联网上提供的一些代码托管服务来搭建Git远程仓库，其中比较常见的有GitHub，码云，GitLab等。

&nbsp;  &nbsp;  &nbsp;  &nbsp;GitHub是一个面向开源即私有软件项目的托管平台，因为只支持Git作为唯一的版本库格式进行托管，因此命名为GitHub；码云是国内的一个代码托管平台；GitLab是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务。
<br>



### （二）	使用码云进行代码托管
#### 1.	创建新仓库
&nbsp;  &nbsp;  &nbsp;  &nbsp;登录码云，创建新的仓库，每个Git远程仓库都会对应一个网络地址，可以店家克隆/下载按钮弹出窗口，窗口中就是Git远程仓库对应的网络地址。
<br>



#### 2.	邀请其他用户成为仓库成员
&nbsp;  &nbsp;  &nbsp;  &nbsp;项目的开发是由多人共同完成的，为了使多个参与者都有权限操作远程仓库，需要邀请其他项目参与者成为当前仓库的成员。
<br>




## 三．	Git常用命令
### （一）	环境配置
&nbsp;  &nbsp;  &nbsp;  &nbsp;安装完Git后首先要设置用户名和email名称，每次Git提交时都会使用该用户名和email地址进行验证。
<br>



#### 1.	设置用户信息

```cpp
git config –global user.name “mrwxx”
git config –global user.email hello@wxx.cn
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;git config 是用于Git配置的命令， --global代表着这是全局的， user.name代表着用户名称，user.email代表着email地址。
<br>



#### 2.	查看配置信息
以列表形式显示所有的Git配置：

```cpp
git config –list
```

显示单个Git配置：

```cpp
git config user.name
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们手动设置完Git的配置后，这些配置信息会保存在本地用户的目录下，如本地的C:\Users\Mr.wmxx下面，有一个.gitconfig文件，文件中存储着刚刚配置的信息：
<br>



```cpp
[user]
	name = mrwxx
	email = hello@wxx.cn
```
<br>



### （二）	获取Git仓库
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取Git仓库有两种方式，一种是在本地初始化一个Git仓库，另一种是从远程仓库中克隆一个。
<br>



#### 1.	本地初始化Git仓库
&nbsp;  &nbsp;  &nbsp;  &nbsp;在本地初始化一个Git仓库，就是将本地的一个文件夹作为Git仓库，在某个文件夹下右键进入Git Bash，使用git init命令初始化一个Git仓库。这样，该文件夹下会生成一个.git隐藏文件夹。
<br>



#### 2.	克隆远程仓库
&nbsp;  &nbsp;  &nbsp;  &nbsp;克隆远程仓库到本地中，在本地的文件夹下进入Git Bash，使用git clone 命令，后面加 远程仓库的地址即可。

```cpp
git clone https://gitee.com/mrxx/repos.git
```
<br>



### （三）	工作目录，暂存区以及版本库
#### 1.	版本库
&nbsp;  &nbsp;  &nbsp;  &nbsp;前面所说的.git文件夹就是版本库，版本库中存储了很多的配置信息，日志信息和文件版本信息。
<br>



#### 2.	工作目录（工作区）
&nbsp;  &nbsp;  &nbsp;  &nbsp;包含.git文件夹的目录就是工作目录，主要用于存放开发的代码。
<br>



#### 3.	暂存区
&nbsp;  &nbsp;  &nbsp;  &nbsp;.git文件夹中的index文件就是暂存区，暂存区用于临时保存修改的文件。

&nbsp;  &nbsp;  &nbsp;  &nbsp;开发人员在工作区进行开发，开发修改后的代码通过 git add命令传到暂存区，再通过git commit 命令提交到版本库中。
<br>



### （四）	Git工作目录下文件的两种状态
&nbsp;  &nbsp;  &nbsp;  &nbsp;Git工作目录下的文件存在两种状态，这些文件的状态会随着Git命令的执行而发生变化。

#### 1.	untracked
&nbsp;  &nbsp;  &nbsp;  &nbsp;未跟踪状态，说明它未被纳入版本控制中。
<br>



#### 2.	tracked
&nbsp;  &nbsp;  &nbsp;  &nbsp;已跟踪状态，说明它已经被Git纳入版本控制了，且已跟踪状态又分为三种状态：
##### （1）	Unmodified 
&nbsp;  &nbsp;  &nbsp;  &nbsp;未修改状态，文件没有被修改过。

##### （2）	Modified
&nbsp;  &nbsp;  &nbsp;  &nbsp;已修改状态，文件已经被修改过了。

##### （3）	Staged
&nbsp;  &nbsp;  &nbsp;  &nbsp;已暂存状态，文件已经放入暂存区中、
<br>



### （五）	本地仓库操作

#### 1.	git status
&nbsp;  &nbsp;  &nbsp;  &nbsp;在工作区中使用git status查看工作区中文件的状态。
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用 git status -s使得输出结果更加简洁

<br>


#### 2.	git add
&nbsp;  &nbsp;  &nbsp;  &nbsp;git add命令将未跟踪的文件加入暂存区中，那么该文件的状态就会变为已跟踪中的已暂存状态。

```cpp
git add hello.txt
```
<br>



#### 3.	git reset
&nbsp;  &nbsp;  &nbsp;  &nbsp;git reset命令将暂存区中的文件取消暂存，那么该文件的状态就会发生变化，变为未跟踪。

```cpp
git reset hello.txt
```
<br>



#### 4.	git commit
&nbsp;  &nbsp;  &nbsp;  &nbsp;要想将代码提交到本地仓库中，必须通过暂存区，即先将代码通过git add命令存到暂存区中，再通过git commit命令将代码提交到本地仓库中。

```cpp
git commit -m “init hello.txt”
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-m代表着日志，每次上传代码到本地仓库中，必须要写日志信息，如果不加-m，它会弹出一个编辑器让我们进行编写。
<br>



#### 5.	git rm
&nbsp;  &nbsp;  &nbsp;  &nbsp;git rm命令将文件从工作区中删除，但是，本地仓库中的该文件并没有被删除，要想删除，需要提交到本地仓库。提交之前我们需要将该文件添加到暂存区中，有趣的是，使用git rm命令删除该文件后，该文件就自动被添加到了暂存区中，因此我们可以直接使用git commit命令提交就可以了，本地仓库中的该文件就被删除了。
<br>



#### 6.	.ignore文件
&nbsp;  &nbsp;  &nbsp;  &nbsp;工作区中有一些文件是不需要纳入Git的管理的，也不希望他们总是出现在未跟踪文件列表中，因此，我们可以在工作区中创建一个名为 .gitignore的文件，在该文件中列出需要忽略的文件模式，如下所示：

*.a  : 代表着忽略所有的.a格式文件

!lib.a : 代表着除了lib.a之外

/TODO : 代表着忽略TODO文件

build/ : 代表着忽略build文件夹下的所有文件

doc/*.txt : 代表着忽略doc文件夹下的所有txt文件

doc/**/*.txt : 代表和忽略doc目录下的所有子目录的txt文件
<br>



#### 7.	git log
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过git log命令查看日志记录，按Enter键向下浏览记录，按q键 退出到命令行。
<br>



### （六）	远程仓库操作
#### 1.	git remote
&nbsp;  &nbsp;  &nbsp;  &nbsp;当一个本地的工作空间和远程仓库建立了联系后，通过git remote命令查看已经配置的远程仓库服务器，它会列出指定的每一个远程服务器的简写，如果已经克隆了远程仓库，那么至少有一个origin，这是Git克隆的远程仓库服务器的默认名字。

&nbsp;  &nbsp;  &nbsp;  &nbsp;想要更加详细的信息，可以在git remote命令后面加上 -v，可以显示远程仓库服务器的地址：

```cpp
git remote -v
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;若是想查看某一个远程仓库服务器的信息，则使用git remote show 命令后面加上该仓库的简写即可：

```cpp
git remote show origin
```
<br>



#### 2.	git remote add
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过git remote add命令为本地添加远程仓库，命令后面首先加上 远程仓库的简写，然后是 远程仓库的地址：

```cpp
git remote add origin https://gitee.com/mtwxx/repos.git
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;我们可以为本地添加多个远程仓库。
<br>



#### 3.	git clone
&nbsp;  &nbsp;  &nbsp;  &nbsp;git clone命令负责从远程仓库中进行拷贝，Git克隆的是该Git仓库服务器上几乎所有数据（包括日志信息，历史记录等）。当执行git clone 命令时，默认配置下远程Git仓库中的每一个文件的每一个版本都会被拉取下来。

```cpp
git clone https://gitee.com/mrwxx/repo.git
```
<br>



#### 4.	git remote rm
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过git remote rm 命令移除本地连接的一个远程仓库，命令后面跟远程仓库的简称。这里只是在本地中移除远程仓库的记录，不会影响到真正的远程仓库。

```cpp
git remote rm origin
```
<br>



#### 5.	git fetch
&nbsp;  &nbsp;  &nbsp;  &nbsp;git fetch是从远程仓库获取最新版本到本地仓库中，不会自动地merge，不会自动合并到本地仓库中，即不会直接显示在本地仓库中。

```cpp
git fetch origin master
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;origin是远程仓库的简称，master是远程仓库的分支。

&nbsp;  &nbsp;  &nbsp;  &nbsp;我们还要通过 git merge 来合并代码到工作区中，后面接远程仓库的分支，如：

```cpp
git merge origin/master
```
<br>



#### 6.	git pull
&nbsp;  &nbsp;  &nbsp;  &nbsp;git pull也是从远程仓库获取最新版本到本地仓库，但是会自动merge，合并代码。

```cpp
git pull origin master
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意：如果当前的本地仓库是本地创建的仓库，且仓库中存在文件，此时再从远程仓库中拉取文件时会报错（fatal:refusing to merge unrelated histories），这是因为原有的文件与拉取的文件无法合并。可以通过在git pull命令后添加参数 –allow-unrelated-histories来解决

```cpp
git pull origin master –allowed-histories-histories
```
<br>



#### 7.	git push
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过git push命令将本地的代码推送到远程仓库中，后面接远程仓库的简写，以及分支名：

```cpp
git push origin master
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这个操作一般都是在我们对之前下载的代码进行修改之后，修改后的代码是没有进入本地仓库的，因此我们首先要将修改后的代码提交到本地仓库，而提交到本地仓库首先要存到暂存区，使用git add 添加到暂存区或者 git commit -a命令直接提交到本地仓库中。在这之后，就可以执行git push命令推送新的代码到远程仓库了。
<br>



### （七）	Git分支
&nbsp;  &nbsp;  &nbsp;  &nbsp;所有的版本控制系统都有分支，使用分支意味着可以把工作从开发主线上分离开来，以免影响开发主线。Git的master分支不是特殊的，之所以每个仓库都有master分支，是因为git init会默认创建它。
#### 1.	查看分支
`git branch`  列出本地所有分支

`git branch -r` 列出所有远程分支

`git branch -a` 列出本地和远程的所有分支
<br>



#### 2.	创建分支

`git branch a` 创建a分支
<br>



#### 3.	切换分支
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们通过git checkout来切换分支，命令行中会显示目前使用的分支：

`git checkout a` 切换到a分支
<br>



#### 4.	推送到远程仓库分支
&nbsp;  &nbsp;  &nbsp;  &nbsp;将本地的分支推送到远程仓库的分支中，如推送到origin仓库的b1分支中：

```cpp
git push origin b1
```
<br>



#### 5.	合并分支
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过`git merge`命令合并两个分支，如我们要将b3分支合并到master分支上，那么，我们需要切换到master分支中，使用git merge b1命令合并分支。如果在两个不同的分支中，对同一个文件同一个部分进行了不同的修改，Git就合并它们，会提示文件冲突。此时我们需要打开冲突的文件并修复冲突内容，最后执行git add命令将代码存到暂存区中。
<br>



#### 6.	删除分支
&nbsp;  &nbsp;  &nbsp;  &nbsp;若要删除本地的分支，可以通过`git branch -d` 后面接分支的名称来删除该分支。若在该分支中已经进行了修改，且没有推送到远程仓库中，那么使用上面的命令并不会删除分支，因为直接删除会有丢失修改数据的风险。若坚持要直接删除，将-d参数改为-D参数即可删除。

&nbsp;  &nbsp;  &nbsp;  &nbsp;若要删除远程仓库中的分支，可以使用`git push origin -d branchName`，origin是远程仓库的简称，branchName是分支的名称。
<br>



### （八）	Git标签
&nbsp;  &nbsp;  &nbsp;  &nbsp;Git可以给历史中的某一个提交打上标签，以示重要，人们会使用这个功能来标记发布节点。标签指的是某个分支某个特定时间点的状态，通过标签，可以很方便地切换到标记时的状态。
#### 1.	git tag
&nbsp;  &nbsp;  &nbsp;  &nbsp;列出已有的标签
<br>



#### 2.	git show [tag]
&nbsp;  &nbsp;  &nbsp;  &nbsp;查看tag信息
<br>



#### 3.	git tag [tagName]
&nbsp;  &nbsp;  &nbsp;  &nbsp;新建一个tag标签
<br>



#### 4.	git push [remote] [tag]
&nbsp;  &nbsp;  &nbsp;  &nbsp;将标签推送至远程仓库，提交指定tag
如： `git push origin v0.1`
<br>



#### 5.	git checkout -b [branch] [tag]
&nbsp;  &nbsp;  &nbsp;  &nbsp;检出标签，新建一个分支，将该分支指向某个tag
<br>



#### 6.	git tag -d [tag]
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除本地标签，
<br>



#### 7.	git push [remote] :refs/tags/[tag]
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除远程仓库的tag，:ref/tags是固定写法：

```cpp
git push origin :refs/tags/[tag]
```








