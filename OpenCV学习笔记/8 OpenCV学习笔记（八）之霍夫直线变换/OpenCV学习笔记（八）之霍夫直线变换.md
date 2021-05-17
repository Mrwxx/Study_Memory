## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 霍夫直线变换介绍
1. Hough Line Transform用来做直线检测
2. 前提条件--边缘检测已经完成
3. 平面空间到极坐标空间转换 

&nbsp;  &nbsp;  &nbsp;  &nbsp;在图像平面空间上的多个点映射到(r-θ)坐标空间中，形成多条曲线。多条曲线有共同的交点，即表明这几个点在图像平面空间中是共线的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200530224641183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
&nbsp;  &nbsp;  &nbsp;  &nbsp;**标准的霍夫变换**cv::HoughLines从平面坐标转换到霍夫空间，最终输出是(θ, r)表示极坐标空间。
&nbsp;  &nbsp;  &nbsp;  &nbsp;**霍夫变换直线概率**cv::HoughLinesP最终输出是直线的两个点(x0, y0, x1, y1)。

## 二. 霍夫变换API介绍
### 1. **cv::HoughLines**
```cpp
cv::HoughLines(
	InputArray src,		//输入图像，必须是8-bit的灰度图像
	OutputArray lines,	//输出的极坐标表示直线
	double rho,				//生成极坐标的像素扫描步长
	double theta,			//生成极坐标的角度步长
	int threshold,			//阈值，只有获得足够交点的极坐标点才能看成是直线
	double srn = 0,		//是否应用多迟钝霍夫变换，如果不是设置0表示经典霍夫变换
	double stn = 0,		//是否应用多迟钝霍夫变换，如果不是设置0表示经典霍夫变换
	double min_theta = 0,	//表示角度扫描范围0-180之间
	double max_theta = CV_PI
)
```
### 2. **cv::HoughLineP**

```cpp
cv::HoughLinesP(
	InputArray src,		//输入图像，必须是8-bit的灰度图像
	OutputArray lines,	//输出的极坐标表示直线
	double rho,				//生成极坐标的像素扫描步长
	double theta,			//生成极坐标的角度步长
	int threshold,			//阈值，只有获得足够交点的极坐标点才能看成是直线
	double minLineLength=0,	//最小直线长度
	double maxLineGap=0	//最大间隔
)
```
## 三. 霍夫变换应用代码：

```cpp
/*****霍夫变换*****/


#include<iostream>
#include<opencv2/opencv.hpp>
#include<string>
#include<vector>
using namespace std;

string in_title = "input image";
string ou_title = "output image";

int main() {
	cv::Mat src, dst,gray_src;
	src = cv::imread("1.jpg");
	cv::namedWindow(in_title, cv::WINDOW_NORMAL);
	cv::imshow(in_title, src);

	//先提取边缘
	cv::Canny(src, gray_src, 150, 200);
	cv::namedWindow("edge image", cv::WINDOW_NORMAL);
	cv::imshow("edge image", gray_src);

	//储存霍夫变换后的直线点坐标，两对
	vector<cv::Vec4f> plines;
	cv::HoughLinesP(gray_src, plines, 1, CV_PI / 180.0, 10, 0, 10);
	//定义颜色
	cv::Scalar color = cv::Scalar(0, 0, 255);
	//画出直线
	cv::cvtColor(gray_src, dst, cv::COLOR_GRAY2BGR);
	for (size_t i = 0; i < plines.size(); ++i) {
		cv::Vec4f hline = plines[i];
		cv::line(dst, cv::Point(hline[0], hline[1]), cv::Point(hline[2], hline[3]),
			color, 3, cv::LINE_AA);
	}
	cv::namedWindow(ou_title, cv::WINDOW_NORMAL);
	cv::imshow(ou_title, dst);

	cv::waitKey(0);
	return 0;
}

```


