## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 卷积应用-图像边缘提取
### (一). 边缘是什么？
&nbsp;  &nbsp;  &nbsp;  &nbsp;边缘是像素值发生跃迁的位置，是图像的显著特征之一，在图像特征提取，对象检测，模式识别等方面都有重要的作用。
### (二). 如何提取边缘？
&nbsp;  &nbsp;  &nbsp;  &nbsp;对图像求一阶导数。一阶导数越大，说明像素在该方向的变化越大，边缘信号越强。

## 二. Sobel算子

 1. 离散微分算子，用来计算图像灰度的近似梯度。 
 2. Sobel算子功能集合高斯平滑和微分求导
 3. 又被称为一阶微分算子，求导算子，在水平和垂直两个方向上求导，得到图像X方向和Y方向的梯度图像。


<center>
<img src="https://img-blog.csdnimg.cn/2020052920260449.png"   width="70%">


<center>
<img src="https://img-blog.csdnimg.cn/20200529202626748.png"   width="70%">

&nbsp;  &nbsp;  &nbsp;  &nbsp;**改进版的Scharr函数，算子如下：**

<center>
<img src="https://img-blog.csdnimg.cn/20200529202733480.png"   width="70%">

## 三. **Sobel算子API：**

```cpp
cv::Sobel(
	InputArray src,			//输入图像
	OutputArray dst,		//输出图像，大小与输入图像一致
	int depth,				//输出图像深度
	int dx,					//X方向，几阶导数
	int dy,					//Y方向，几阶导数
	int ksize,				//Sobel算子kernel大小，必须是1,3,5，7
	doublle scale = 1,	
	double delta = 0.
	int borderType = BORDER_DEFAULT
)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;**输出图像的深度至少是跟输入图像持平的。**
cv::Scharr的参数与Sobel相同。

## 四. 下面是代码：
&nbsp;  &nbsp;  &nbsp;  &nbsp;**Sobel算子的算法实现**
```cpp
/*****Sobel算子*****/

#include<iostream>
#include<opencv2/opencv.hpp>
#include<string>
using namespace std;

string in_title = "input image";
string ou_title = "output image";
int main() {
	cv::Mat src, dst;
	src = cv::imread("1.jpg");
	cv::namedWindow(in_title, cv::WINDOW_NORMAL);
	cv::imshow(in_title, src);

	cv::Mat gray_src;
	//先高斯模糊
	cv::GaussianBlur(src, dst, cv::Size(3, 3), 0, 0);
	//再转成灰度图
	cv::cvtColor(dst, gray_src, cv::COLOR_BGR2GRAY);
	cv::imshow("gray image", gray_src);

	cv::Mat xgrad, ygrad;
	//Sobel
	//cv::Sobel(gray_src, xgrad, CV_16S, 1, 0, 3);
	//cv::Sobel(gray_src, ygrad, CV_16S, 0, 1, 3);
	//Scharr
	cv::Scharr(gray_src, xgrad, CV_16S, 1, 0, 3);
	cv::Scharr(gray_src, ygrad, CV_16S, 0, 1, 3);
	//将负值转正
	cv::convertScaleAbs(xgrad, xgrad);
	cv::convertScaleAbs(ygrad, ygrad);

	cv::namedWindow("xgrad", cv::WINDOW_NORMAL);
	cv::namedWindow("ygrad", cv::WINDOW_NORMAL);

	cv::imshow("xgrad", xgrad);
	cv::imshow("ygrad", ygrad);

	cv::Mat xygrad;
	xygrad = cv::Mat(xgrad.size(), xgrad.type());
	//用每个像素直接进行计算
	int width = xgrad.cols;
	int height = xgrad.rows;
	for (int row = 0; row < height; ++row) {
		for (int col = 0; col < width; ++col) {
			int xg = xgrad.at<uchar>(row, col);
			int yg = ygrad.at<uchar>(row, col);
			int xy = xg + yg;
			xygrad.at<uchar>(row, col) = cv::saturate_cast<uchar>(xy);
		}
	}
	//或者使用权重函数相加
	//cv::addWeighted(xgrad, 0.5, ygrad, 0.5, 0, xygrad);
	cv::namedWindow("xygrad", cv::WINDOW_NORMAL);
	cv::imshow("xygrad", xygrad);

	cv::waitKey(0);
	return 0;
}
```

