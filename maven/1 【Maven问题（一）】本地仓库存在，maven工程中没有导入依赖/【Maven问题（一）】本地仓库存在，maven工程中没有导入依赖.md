## 一．	问题场景

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在pom中导入一个依赖，并且maven本地仓库中已经将这个依赖下载下来了，可是maven project中依然找不到这个对应的依赖，检查External Library中也没有这个依赖，说明Maven根本没有识别这个依赖，也就是说maven工程中没有这个依赖。
<br>


## 二．	解决方法尝试

### （一）	clean + install + reimport

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在该工程的Lifecycle生命周期中先进行clean清理操作，再进行install打包操作，最后点击该工程右键，reimport，问题没有解决。
<br>


### （二）	本地maven仓库

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于maven工程根本就没有导入这个依赖，就有可能是maven本地仓库的问题，查看该jar包对应的目录，有一个_remote.repositories文件。maven3.x版本从远程仓库下载资源后，会生成对应的_remote.repositories文件，标示该资源的来源，如果你有这个文件_remote.repositories，那就不会访问本地了，必须远程上有才行，否则就会报错。因此，这里我删除了该文件，重新reimport项目，最终问题解决。



