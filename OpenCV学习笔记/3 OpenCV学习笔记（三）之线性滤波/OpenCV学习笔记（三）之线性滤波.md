## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 卷积概念
&nbsp;  &nbsp;  &nbsp;  &nbsp;卷积是图像处理中的一个操作，是kernel在图像的每个像素上的操作。kernel本质上是一个固定大小的矩阵数组，其中心点称为锚点（anchor point）。
## 二. 卷积如何工作？
&nbsp;  &nbsp;  &nbsp;  &nbsp;把kernel放到像素数组上，求锚点周覆盖的像素乘积之和（包括锚点），用来替换锚点覆盖下像素点值称为卷积处理。
## 三. 常见卷积算子

 ### 1. **Robert 算子**


<center>
<img src="https://img-blog.csdnimg.cn/20200528224447511.png"   width="70%">

&nbsp;  &nbsp;  &nbsp;  &nbsp;左图是左斜45度方向的差异，右图是右斜45度方向的差异。

 ### 2. Sobel 算子

<center>
<img src="https://img-blog.csdnimg.cn/20200528224516713.png"   width="70%">

&nbsp;  &nbsp;  &nbsp;  &nbsp;左图是水平方向的像素差异，右图是垂直方向的像素差异。

### **3. 拉普拉斯算子**

<center>
<img src="https://img-blog.csdnimg.cn/20200528224536149.png"   width="60%">

## 四. 自定义线性滤波
&nbsp;  &nbsp;  &nbsp;  &nbsp;自己定义一个线型滤波器的API  cv::filter2D

```cpp
cv::filter2D(
	Mat src,	//输入图像
	Mat dst,	//输出图像
	int depth,	//图像深度32/8
	Mat kernel,	//卷积核/模板
	Point anchor,//锚点位置
	double delta,//计算出来的像素+delta
	)
```
## 下面是代码：

```cpp
/*****线性滤波*****/

#include<opencv2/opencv.hpp>
#include<iostream>
#include<string>
using namespace std;

int main() {
	cv::Mat src, dst;
	src = cv::imread("1.jpg");
	string input_title = "input image";
	string output_title = "output image";
	
	cv::namedWindow(input_title, cv::WINDOW_NORMAL);
	cv::namedWindow(output_title, cv::WINDOW_NORMAL);
	cv::imshow(input_title, src);

	//Robert X 方向
	cv::Mat robert_x = (cv::Mat_<int>(2, 2) << 1, 0, 0, -1);
	//线性滤波
	cv::filter2D(src, dst, -1, robert_x, cv::Point(-1, -1), 0.0);

	//Robert Y 方向
	cv::Mat robert_y = (cv::Mat_<int>(2, 2) << 0, 1, -1, 0);
	//线性滤波
	cv::filter2D(src, dst, -1, robert_y, cv::Point(-1, -1), 0.0);

	//Sobel X 方向
	cv::Mat sobel_x = (cv::Mat_<int>(3,3) << -1,0,1,-2,0,2,-1,0,1);
	//线性滤波
	cv::filter2D(src, dst, -1, sobel_x, cv::Point(-1, -1), 0.0);

	//Sobel Y 方向
	cv::Mat sobel_y = (cv::Mat_<int>(3,3) << 0,-1,0,-1,4,-1,0,-1,0);
	//线性滤波
	cv::filter2D(src, dst, -1, sobel_y, cv::Point(-1, -1), 0.0);

	//拉普拉斯算子
	cv::Mat lap = (cv::Mat_<int>(3,3) << 0,-1,0,-1,4,-1,0,-1,0);
	//线性滤波
	cv::filter2D(src, dst, -1, lap, cv::Point(-1, -1), 0.0);

	cv::imshow(output_title, dst);


	cv::waitKey(0);
	return 0;

}
```

