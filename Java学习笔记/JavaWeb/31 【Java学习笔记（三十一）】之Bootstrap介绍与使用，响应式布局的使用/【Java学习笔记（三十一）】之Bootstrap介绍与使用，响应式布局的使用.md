## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Bootstrap
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;一个前端开发的框架，Bootstrap，来自 Twitter，是目前很受欢迎的前端框架。Bootstrap 是基于 HTML、CSS、JavaScript 的，它简洁灵活，使得 Web 开发更加快捷。
<br>


### （二）	优势
&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	Bootstrap框架定义了很多的css样式和js插件，可以直接使用得到多样的页面效果。

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	响应式布局：同一个页面可以兼容不同分辨率的设备。
<br>




## 二. 使用Bootstrap
### （一）	下载Bootstrap

### （二）	导入Bootstrap文件到项目汇总

### （三）	创建HTML页面，导入必须的资源
<br>



```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>Bootstrap HelloWorld</title>

    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">


    <!-- jQuery (Bootstrap 的所有 JavaScript 插件都依赖 jQuery，所以必须放在前边) -->
    <script src="js/jquery-3.2.1.min.js"></script>
    <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
    <script src="js/bootstrap.min.js"></script>
</head>
<body>
<h1>你好，世界！</h1>
</body>
</html>
```
<br>



## 三．	响应式布局
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;在同一套页面中可以兼容不同分辨率的设备。
<br>



### （二）	响应式布局的实现
#### 1.	实现原理
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过栅格系统实现，将页面的每一行分为12个格子，指定每种元素在每种分辨率尺寸的设备中占几个格子。这样，元素的显示就能适应多种设备的分辨率。
<br>



#### 2.	容器
&nbsp;  &nbsp;  &nbsp;  &nbsp;容器相当于表格的table标签，不过是作为class属性，分为两种：

&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	container: 几乎占满一行，两边些许留白。

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	container-fluid：在每一种设备上都是100%宽度，全部占满一行。

```html
<div class=”container”></div>
```
<br>



#### 3.	行
&nbsp;  &nbsp;  &nbsp;  &nbsp;行相当于表格中的tr标签，样式为row，同样是作为class属性。

```html
<div class=”row”></div>
```
<br>



#### 4.	元素
&nbsp;  &nbsp;  &nbsp;  &nbsp;每行中的元素，指定该元素在不同的设备中所占的格子数目。样式：col-设备代号-格子数目

设备代号：

```html
xs：超小屏幕 手机 (<768px)
sm：小屏幕 平板 (≥768px) 
md：中等屏幕 桌面显示器 (≥992px) 
lg：大屏幕 大桌面显示器 (≥1200px)
```

```html
<div class=”col-lg-1 col-sm-2”>栅格</div>
```
<br>




## 四．	css样式和js插件
### （一）	全局css样式
#### 1.	按钮
&nbsp;  &nbsp;  &nbsp;  &nbsp;class=”btn btn-default”
<br>



#### 2.	图片
&nbsp;  &nbsp;  &nbsp;  &nbsp;class=”img-responsive”，响应式布局，任意分辨率都占满一行
<br>



#### 3.	表格
&nbsp;  &nbsp;  &nbsp;  &nbsp;class=”table table-bordered table hover”
<br>



#### 4.	表单
&nbsp;  &nbsp;  &nbsp;  &nbsp;class=”form-control”
<br>



### （二）	导航条
&nbsp;  &nbsp;  &nbsp;  &nbsp;class=”navbar navbar default”


