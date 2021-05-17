## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	帮助命令
### （一）	man 命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过man命令查看其它命令的详细文档。

#### 1.	命令格式

```cpp
man [选项] 命令
```

```cpp
-f : 查看命令拥有哪个级别的帮助
-k：查看和命令相关的所有帮助
```

```cpp
man -f ls
```

#### 2.	命令的帮助级别
&nbsp;  &nbsp;  &nbsp;  &nbsp;不同的命令有不同的帮助级别，使用man查看命令的文档时可以看到命令后的数字就是该命令的帮助级别。

&nbsp;  &nbsp;  &nbsp;  &nbsp;1： 普通用户可执行的系统命令和可执行文件的帮助

&nbsp;  &nbsp;  &nbsp;  &nbsp;2： 内核可以调用的函数和工具的帮助

级别过多，懒得记录…

### （二）	info命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;最完整的命令资料，如同天书。

### （三）	help 命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;help只能获取Shell内置命令的帮助， 可以使用type命令来区分内置命令和外部命令。

&nbsp;  &nbsp;  &nbsp;  &nbsp;cd是shell内置命令，使用
`type cd`就会展示如下内容，表示cd是Shell内置命令：

```cpp
cd is a shell builtin
```

### （四）	--help选项
&nbsp;  &nbsp;  &nbsp;  &nbsp;这种方法比较简单，输出的帮助信息基本上时man命令的信息简要版。

```cpp
ls --help
```


## 二．  搜索命令
### （一）whereis命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;只能搜索系统命令，不能搜索普通文件。在搜索系统命令时，可以查找出二进制命令，命令的源文件位置，帮助文档。

```cpp
whereis ls
```

### （二） which命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;which命令在查找二进制命令的同时，如果该命令有别名，还可以找到别名。

如下所示：

```cpp
which ls
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查找系统的别名命令为alias，可以查找到如 ls -l –color=auto 命令的别名是ll。

### （三） locate命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;按照文件名搜索普通文件，优点是按照数据库搜索，搜索速度极快，消耗资源小。数据库的位置在/var/lib/mlocate/mlocate.db，当搜索不到文件的时候，可能是数据库未更新，可以使用updatedb命令更新数据库。

&nbsp;  &nbsp;  &nbsp;  &nbsp;配置文件在 /etc/updated.conf中，可以配置搜索的禁止选项，如下所示：


```cpp
PRUNE_BIND_MOUNTS = “yes” # 开启搜索限制，让配置文件生效
PRUNEFS = “…” #禁止搜索的系统文件类型
PRUNENAMES = “…” #禁止搜索的扩展名
PRUNEPATHS = “…” #禁止搜索的系统目录
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这就可以解释为什么我们搜不到有些目录的文件了，因为都在配置文件中禁止了。

### （四）	find命令
#### 1.	按照文件名搜索
格式如下：

```cpp
find 搜索路径 [选项] 搜索内容
```

选项：

```cpp
-name: 按照文件名搜索
-iname: 不区分大小写，搜索文件名
-inum: 按照文件的I节点号搜索
```

#### 2.	按照文件大小搜索
格式如下：

```cpp
find 搜索路径 [选项] 搜索内容
```

选项：
&nbsp;  &nbsp;  &nbsp;  &nbsp;-size [+|-]大小： 按照指定大小搜索文件，“+”代表比指定大小大的文件，“-”代表比指定大小小的文件。

&nbsp;  &nbsp;  &nbsp;  &nbsp;搜索大小的单位默认是“b”，表示512个字节；“c”表示字节；“w”表示两个字节（中文）；“k”表示KB，注意是小写；M表示MB，注意是大写；G表示GB，注意是大写。

```cpp
find . -size +4096c
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;表示在本目录下搜索大于4096字节的文件或目录。

#### 3.	按照修改时间搜索
&nbsp;  &nbsp;  &nbsp;  &nbsp;Linux中的文件有访问时间（atime）, 数据修改时间（mtime）， 状态修改时间（ctime）三种时间，选项就是三种时间的缩写。

选项：

```cpp
-atime [+|-] 时间
-mtime [+|-] 时间
-ctime [+|-] 时间
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，这里的+-时间比较特殊，-5代表着5天内修改的文件，单独的5代表着5-6天那天修改的文件，+5代表着6天前修改的文件。

#### 4.	按照权限搜索
格式：

```cpp
find 搜索路径 [选项] 搜索内容
```

选项：

```cpp
-perm 权限： 查找文件权限刚好等于该权限的文件
-perm -权限:  查找文件权限中每个权限都大于给出的权限的文件
-perm +权限：查找文件权限中大于任意一个权限的文件。
```

如：

```cpp
find . -perm +456 
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;找所属用户权限，所属组权限，其他人权限中任一大于对应权限的文件即可。

```cpp
find . -perm -456
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;找出所属用户权限，所属组权限，其他人权限中都要大于对应权限的文件。

#### 5.	按照所有者和所属组搜索

选项：

```cpp
-uid 用户ID
-gid 组ID
-user 用户名
-group 组名
-nouser 查找没有所有者的文件
```

```cpp
find . -user wxx
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-nouser选项是最常用的，用于查找垃圾文件，还有外来文件或者是手工源码包安装的文件。

#### 6.	按照文件类型搜索

选项：

```cpp
-type d: 查找目录
-type f: 查找普通文件
```

```cpp
find . -type d
```

#### 7.	逻辑运算符
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用find搜索时，可以使用逻辑运算符来整合两个搜索条件。

选项：

```cpp
-a : 逻辑与
-o：逻辑或
-not: 逻辑非
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;逻辑与即表示搜索的两个条件要同时满足，如下所示：

```cpp
find . -size +2k -a -type f
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;在当前目录下搜索大于2KB，且文件类型是普通文件的文件。

#### 8.	其他选项

##### （1）	-exec选项
&nbsp;  &nbsp;  &nbsp;  &nbsp;该选项将find命令的搜索结果交由-exec调用的第二条命令来处理，{}就代表着find命令查找结果，\和-exec配合使用。

```cpp
find . -size +2k -exec ls -lh {}\;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;表示将搜索大小大于2KB的文件信息详细地展示出来。

##### （2）	-ok选项
&nbsp;  &nbsp;  &nbsp;  &nbsp;与-exec基本一致，不过-ok选项在每次执行时会询问用户是否这样做。

### （六）	grep命令
#### 1.	格式
&nbsp;  &nbsp;  &nbsp;  &nbsp;grep命令是在文件中提取出和匹配符合条件的字符串行。

```cpp
grep  [选项] “搜索内容” 文件名
```

```cpp
grep “[0-9]$” abc
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这里使用正则表达式在abc文件中查找以数字结尾的字符行。

#### 2.	选项

```cpp
-I ： 忽略大小写
-n：  输出行号
-v：  反向查找，即与条件相反
--color=auto: 搜索出的关键字用颜色突出
```

#### 3.	grep与find的区别
&nbsp;  &nbsp;  &nbsp;  &nbsp;find命令只能搜索文件名，如果需要模糊查询，则使用通配符进行匹配，通配符是完全匹配，是严格相等的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;grep命令在文件中搜索符合条件的字符串，若需要模糊查询，可以使用正则表达式匹配，正则式包含匹配的，并不需要严格相等，只需要包含即可。







