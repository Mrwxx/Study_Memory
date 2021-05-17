## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	系统api与库函数的关系
### （一）调用库函数的流程
&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们使用一些库函数，如printf(“hello world”)时，调用了文件指针 FILE *stdout, 该文件指针的组成为：

```cpp
fd	文件描述符
f_pos 位置
buffer 8k的缓冲区
```
<br>


&nbsp;  &nbsp;  &nbsp;  &nbsp;如，调用printf(“hello\n”)后，系统会调用write(1, “hello\n”, 6)，接着进入内核调用sys_write(“hello\n”)，再通过设备驱动显示在显示器中。
<br>



### （二）内核中的管理
&nbsp;  &nbsp;  &nbsp;  &nbsp;内核中有虚拟文件系统，内存管理，进程管理，设备管理，PCB进程控制块，文件描述符表。
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;文件描述符表中存的就是文件指针中的文件描述符，如描述符0对应的是FILE * stdin，1对应的是 FILE *stdout，2对应的是 FILE * stderr，因此，上面调用printf(“hello\n”)，系统会自动调用write(1, “hello\n”, 6)中的1指的就是FILE *stdout，写到标准输出上。

<br>



## 二．	文件IO
### （一）	open函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过查看man文档的第二页来了解open函数：man 2 open。该函数用来打开文件。
<br>



#### 1.	函数原型

```cpp
int open(const char *pathname, int flags);
int open(const char *pathname, int flags);
```
<br>



#### 2.	函数参数
##### （1）pathname
&nbsp;  &nbsp;  &nbsp;  &nbsp;文件名
<br>



##### （2）flags:
必选项：

```cpp
	O_RDONLY:只读
 O_WRONLY:只写
 O_RDWR:读写
```
<br>



可选项：

```cpp
O_APPEND:追加
O_CREATE ; 创建文件
O_NONBLOCK:非阻塞
```
<br>



#### 3.	函数的返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp;成功时返回最小的可用文件描述符，失败时返回-1.设置errno。
<br>



### （二）	close函数
#### 1.	用途
&nbsp;  &nbsp;  &nbsp;  &nbsp;关闭一个文件描述符
<br>



#### 2.	函数原型

```cpp
int close(int fd);
```
<br>



#### 3.	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp;成功时返回0，错误时返回-1、设置errno。
<br>



### （三）	read
&nbsp;  &nbsp;  &nbsp;  &nbsp;读取文件

<br>

#### 1.	函数原型

```cpp
size_t read(int fd, void* buf, size_t count);
```
<br>



#### 2.	函数参数

```cpp
fd : 文件描述符
buf: 缓冲区
count ：缓冲区大小
```
<br>



#### 3.	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp;成功时返回读到的大小

&nbsp;  &nbsp;  &nbsp;  &nbsp;失败时返回-1，并设置errno

&nbsp;  &nbsp;  &nbsp;  &nbsp;返回0表示读到了文件末尾
<br>



### （四）	write

&nbsp;  &nbsp;  &nbsp;  &nbsp;向文件中写入。
<br>



#### 1.	函数原型

```cpp
size_t write(int fd, const void* buf, size_t count);
```
<br>



#### 2.	函数参数

```cpp
fd: 文件描述符
buf: 缓冲区
count: 缓冲区大小
```
<br>



#### 3.	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp;成功时返回写入的字节数

&nbsp;  &nbsp;  &nbsp;  &nbsp;失败时返回-1，并设置errno

&nbsp;  &nbsp;  &nbsp;  &nbsp;返回0代表未写入
<br>



### （五）	lseek
&nbsp;  &nbsp;  &nbsp;  &nbsp;移动文件的读写位置。
<br>



#### 1.	函数原型

```cpp
off_t lseek(int fd, off_t offset, int whence);
```
<br>



#### 2.	函数参数

```cpp
fd: 文件描述符
offset: 偏移量
whence: 
		SEEK_SET: 文件开始位置
		SEEK_CUR: 当前位置
		SEEK_END: 文件的结尾
```
<br>



#### 3.	函数返回值
&nbsp;  &nbsp;  &nbsp;  &nbsp;成功时返回当前位置到开始的长度

&nbsp;  &nbsp;  &nbsp;  &nbsp;失败时返回-1.并设置errno
<br>



#### 4.	函数的作用
&nbsp;  &nbsp;  &nbsp;  &nbsp;移动文件读写位置

&nbsp;  &nbsp;  &nbsp;  &nbsp;计算文件大小
<br>



#### 5.	lseek实现计算文件大小

```cpp
#include<stdio.h>
#include<sys/types.h>
#include<unistd.h>
#include<sys/stat.h
#include<fcntl.h>

int main(int argc, char *argv[]){
		if(argc != 2){
			printf(“./a.out filename\n”);
			return -1;
		}

		int fd = open(argv[1], 0_RDONLY);
		int ret = lseek(fd, 0, SEEK_END);
		printf(“file size is %d\n”, ret);

		close(fd);
		return 0;
}
```


