## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	fork函数
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; fork函数在一个进程的基础上创建一个新的子进程。
<br>


### （二）	函数原型

```cpp
pid_t fork(void);
```
<br>



### （三）	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建成功时，返回两次。父进程返回子进程的id，子进程返回0.。
<br>



### （四）	getpid
&nbsp;  &nbsp;  &nbsp;  &nbsp; 获得当前进程的id（pid）。

```cpp
pid_t getpid(void);
```
<br>



### （五）	getppid
&nbsp;  &nbsp;  &nbsp;  &nbsp; 获得当前进程的父进程的id（ppid）。

```cpp
pid_t getppid(void);
```
<br>



### （六）	使用示例
<br>



```cpp
#include<stdio.h>
#include<unisted.h>
#include<stdlib.h>

int main(){
	printf(“Begin\n”);
	pid_t pid = fork();
	if(pid < 0){
		perror(“fork err”);
		exit(1);
	}
	//子进程
	if(pid == 0){
		printf(“I am a child, pid=%d, ppid=%d\n”, getpid(), getppid());
	}
	else if(pid > 0){
		printf(“childpid=%d, self=%d, ppid=%d\n”, pid, getpid(), getppid());
		sleep(1);
	}
	printf(“End … \n”);
	return 0;
}
```

<br>



## 二．	进程操作
### （一）	查看进程信息
&nbsp;  &nbsp;  &nbsp;  &nbsp; init进程是所有进程的祖先。

```cpp
ps aux
ps ajx 可以追溯进程之间的父子关系、
```
<br>



### （二）	杀死进程
&nbsp;  &nbsp;  &nbsp;  &nbsp; kill -l 查看信号相关的信息

&nbsp;  &nbsp;  &nbsp;  &nbsp; 给进程发送一个信号，杀死进程的信号为 SIGKILL  9号信号。

```cpp
kill -SIGKILL pid
	pid为进程的id
```

<br>



## 三．	创建多个子进程
&nbsp;  &nbsp;  &nbsp;  &nbsp; 上面已经讲解过了创建单个子进程，直接使用fork函数创建子进程即可。那么，在创建多个子进程时，如果直接使用for循环创建多个子进程就会出现子进程会接着循环创建自己的子进程，即如果循环5次，那么会创建32个子进程，而不是5个子进程。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 要想解决这个问题，就要在确认是新创建的子进程处通过break离开循环。
<br>



```cpp
#include<stdio.h>
#Include<unisted.h>
#include<stdlib.h>

int main(){
	int n =5;
	int i = 0;
	pid_t pid = 0;
	for(I = 0; I < 5; ++i){
		pid = fork();
		if(pid == 0){
			printf(“I am child, pid=%d, ppid=%d\n”, getpid(), getppid());
			break;
		}
		else if(pid > 0){
			printf(“fater, pid=%d, ppid=%d\n”, getpid(), getppid());
		}
	}
	return 0;
}
```
<br>




## 四．	exec函数族
### （一）	execlp函数
#### 1.	用途
&nbsp;  &nbsp;  &nbsp;  &nbsp; 借助PATH环境变量，加载一个进程。
<br>



#### 2.	函数原型

```cpp
int execlp(const char* file, const char * arg,…);
```
<br>



#### 3.	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp; 成功不返回，失败返回-1
<br>



#### 4.	函数参数
&nbsp;  &nbsp;  &nbsp;  &nbsp; **file:**  要加载的程序的名称，该函数需要配合PATH环境变量使用，在环境变量的目录中搜索有无参数1，通常用于调用系统程序，如：ls, cp…

&nbsp;  &nbsp;  &nbsp;  &nbsp; **arg:**  参数列表，参数列表最后需要一个NULL作为结尾
<br>



### （二）	execl函数
#### 1.	用途
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过路径 + 程序名 来加载一个进程。
<br>



#### 2.	函数原型

```cpp
int execl(const char * path, const char * arg, …);
```
<br>



#### 3.	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp; 成功无返回，失败返回-1
<br>



#### 4.	函数参数
&nbsp;  &nbsp;  &nbsp;  &nbsp; 参数与execlp函数一样，只不过path要加上路径名。
<br>



如： 加载 ls 命令并带有 -l,-F 参数

```cpp
execlp(“ls”, “ls”, “-l”, “-F”, NULL);
```

execlp函数在PATH的目录下搜索ls 程序名称
<br>



```cpp
execl(“/bin/ls”, “ls”, “-l”, “-F”, NULL);
```

execl函数在绝对路径中搜索ls 程序名称

<br>



## 五．	孤儿进程与僵尸进程
### （一）	孤儿进程
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该进程的父进程被kill了，该进程被init进程给领养了。
<br>



### （二）	僵尸进程
&nbsp;  &nbsp;  &nbsp;  &nbsp; 子进程被kill了，父进程没有回收子进程的资源，则该子进程成为了僵尸进程。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 那么僵尸进程如何被回收呢？通过kill父进程，init进程领养子进程，init进程负责回收子进程。

<br>



## 六．	wait函数
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; wait函数一般用于回收子进程的资源，如果子进程没有被Kill，则wait函数一直阻塞等待着子进程的死亡，并且能够知晓子进程的死亡原因。
<br>



### （二）	作用
1.	阻塞等待

2.	回收子进程资源

4.	查看死亡原因
<br>



### （三）	函数原型

```cpp
pid_t wait(int * status);
```

status： 传出参数
<br>



### （四）	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp; 成功返回 终止的子进程ID
&nbsp;  &nbsp;  &nbsp;  &nbsp; 失败返回 -1
<br>



### （五）	子进程死亡原因
#### 1.	正常死亡 WIFEXITED
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果WIFEXITED为真，则使用WEXITSTATUS得到退出状态
<br>



#### 2.	非正常死亡 WIFSIGNALED
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果WIFSIGNALED为真，则使用WTERMSIG得到信号

<br>



## 七．	waitpid函数
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 回收子进程。
<br>



### （二）	函数原型

```cpp
pid_t waitpid(pid_t pid, int * status, int options);
```
<br>



### （三） 函数参数
#### 1. pid

```cpp
<-1 传递的是组id
-1 回收任意
0  回收和调用进程组id相同组内的子进程
>0 回收指定的pid
```
<br>



#### 2. options
		

```cpp
0和wait相同，也会阻塞
设置WNOHANG,如果当前没有子进程退出，会立刻返回
```
<br>



### （四） 函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果设置了WNOHANG,那么如果没有子进程退出，则返回0；如果有子进程退出，则返回退出的pid

&nbsp;  &nbsp;  &nbsp;  &nbsp; 失败返回-1（没有子进程）



