## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 霍夫圆检测
&nbsp;  &nbsp;  &nbsp;  &nbsp;因为霍夫圆检测对噪声比较敏感，所以首先要对图像做中值滤波。
&nbsp;  &nbsp;  &nbsp;  &nbsp;基于效率考虑，OpenCV中实现的霍夫变换圆检测是基于图像梯度的实现，分为两步：
1. 检测边缘，发现可能的圆心
2. 在第一步的基础上从候选圆心开始计算最佳半径大小。

## 二. 霍夫圆检测API
&nbsp;  &nbsp;  &nbsp;  &nbsp;**cv::HoughCircles()API分析**
```cpp
cv::HoughCircles(
	InputArray image,	//输入图像，必须是8位的单通道灰度图像
	OutputArray circles,	//圆信息
	Int method,				//方法
	Double dp,				//dp = 1，尺度，在原图上查找为1,
	Double mindist,		//两个圆心相聚的最小距离可以认为是两个圆
	Double param1,		//Canny边缘检测的高阈值
	Double param2,		//中心点累加阈值-候选圆心
	Int minradius,			//最小半径
	Int maxradius,			//最大半径
）
```
## 三. **霍夫圆检测应用代码：**

```cpp
/*****霍夫圆变换*****/

#include<iostream>
#include<opencv2/opencv.hpp>
#include<string>
#include<vector>
using namespace std;

string in_title = "input image";
string ou_title = "output image";

int main() {
	cv::Mat src, dst, gray_src;
	src = cv::imread("检测圆.png");
	cv::namedWindow(in_title, cv::WINDOW_NORMAL);
	cv::imshow(in_title, src);

	//中值滤波
	cv::Mat m_output;
	cv::medianBlur(src, m_output, 3);
	cv::cvtColor(m_output, m_output, cv::COLOR_BGR2GRAY);

	//霍夫圆检测
	//圆的圆心和半径
	vector<cv::Vec3f> p_circles;
	cv::HoughCircles(m_output, p_circles, cv::HOUGH_GRADIENT, 1,
		3, 100, 25, 5, 50);
	src.copyTo(dst);
	//画出圆
	for (size_t i = 0; i < p_circles.size(); ++i) {
		cv::Vec3f cc = p_circles[i];
		cv::circle(dst, cv::Point(cc[0], cc[1]), cc[2], cv::Scalar(0, 0, 255),
			2, cv::LINE_AA);
		cv::circle(dst, cv::Point(cc[0], cc[1]), 2, cv::Scalar(0, 0, 255),
			2, cv::LINE_AA);
	}

	cv::namedWindow(ou_title, cv::WINDOW_NORMAL);
	cv::imshow(ou_title, dst);

	cv::waitKey(0);
	return 0;

}
```


