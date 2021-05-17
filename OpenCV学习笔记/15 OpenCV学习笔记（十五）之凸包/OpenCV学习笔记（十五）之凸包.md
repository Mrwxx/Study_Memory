## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 凸包
&nbsp;  &nbsp;  &nbsp;  &nbsp;在一个多边形的边缘和内部，任意两个点的连线都包含在边缘和内部。该多边形为凸包。

## 二. API分析
### cv::convexHull

```cpp
cv::convexHull(
	InputArray points,		//输入候选点，来自findContours
	OutputArray hull,			//凸包
	Bool clockwise,			//顺时针方向
	Bool returnPoints,		//true表示返回点个数，如果第二个参数是vector<Point>则自动忽略
）
```

## 三. 代码思路：
1. 首先把图像从RGB转为灰度图像
2. 转为二值图像
3. 通过发现轮廓得到候选点
4. 寻找凸包
5. 绘制

## 四. 代码实现：

```cpp
/*****凸包*****/

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace std;

int threshold = 50;
int threshold_max = 150;
cv::Mat src, dst, gray_src;

void Threshold_callback(int, void*);
int main(int argc, char** argv) {
	src = cv::imread("1.jpg");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::namedWindow("ou", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	//转灰度图像
	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);

	//Canny边缘检测
	cv::createTrackbar("Threshold: ", "ou", &threshold, threshold_max,
		Threshold_callback);
	Threshold_callback(0, 0);

	cv::waitKey(0);
	return 0;
}

void Threshold_callback(int, void*) {
	//threshold 输出
	cv::Mat bin_output;			
	cv::threshold(gray_src, bin_output, threshold, threshold_max,
		cv::THRESH_BINARY);
	//轮廓点的vector
	vector<vector<cv::Point>> contours;
	//轮廓的拓扑图,即轮廓的结构层次
	vector<cv::Vec4i> hierarchy;
	
	cv::findContours(bin_output, contours, hierarchy, cv::RETR_TREE,
		cv::CHAIN_APPROX_SIMPLE, cv::Point(0, 0));

	//凸包点的vector
	vector<vector<cv::Point>> convexs(contours.size());
	
	dst = cv::Mat::zeros(src.size(), CV_8UC3);
	//随机数
	cv::RNG rng(12345);
	//在轮廓点的基础上寻找凸包的点
	for (size_t i = 0; i < contours.size(); ++i)
		cv::convexHull(contours[i], convexs[i], false, true);
	//画出轮廓，凸包
	for (size_t k = 0; k < contours.size(); ++k) {
		cv::Scalar color = cv::Scalar(rng.uniform(0, 255), rng.uniform(0, 255),
			rng.uniform(0, 255));
		cv::drawContours(dst, contours, k, color, 2, 8, hierarchy,
			0, cv::Point());
		cv::drawContours(dst, convexs, k, color, 2, 8, hierarchy,
			0, cv::Point());
	}

	cv::imshow("ou", dst);

}
```

