## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 减少多边形轮廓点数
&nbsp;  &nbsp;  &nbsp;  &nbsp;**API : cv::approxPolyDP**
```cpp
cv::approxPolyDP(
	InputArray curve,
	OutputArray approxCurve,
	double epsilon,
	bool closed
)
```

## 二. 绘制包围轮廓矩形
API :  
1. **cv::boundingRect**

```cpp
cv::boundingRect(
	InputArray points
)
```

得到包围轮廓最小矩形的左上角点坐标和右下角点坐标，绘制一个矩形

2. **cv::minAreaRect**
```cpp
cv::minAreaRect(InputArray points)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;得到一个旋转的矩形，返回旋转矩形

## 三. 绘制包围轮廓的圆和椭圆
API :
1.  **cv::minEnclosingCircle**
```cpp
cv::minEnclosingCircle(
InputArray points,
Point2f& center,		//圆心位置
float& radius			//圆的半径
)
```

2. **cv::fitEllipse**
```cpp
cv::fitEllipse(InputArray points)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;得到最小椭圆

## 四. 代码思路：
1. 图像转为二值图像
2. 找到图像轮廓
3. 通过相关API在轮廓点上找到包围轮廓的最小矩形和圆，旋转矩形和椭圆
4. 绘制出来

## 五. 代码实现

```cpp
/*****轮廓周围绘制矩形框和圆形框*****/

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace std;

int threshold = 50;
int threshold_max = 255;
void Contours_Callback(int, void*);
cv::Mat src, dst, gray_src;
cv::RNG rng;

int main(int argc, char** argv) {
	src = cv::imread("矩形，圆形.png");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);
	//模糊，去噪
	cv::blur(gray_src, gray_src, cv::Size(3, 3), cv::Point(-1, -1));

	cv::namedWindow("ou", cv::WINDOW_NORMAL);
	cv::createTrackbar("Threshold Value:", "ou", &threshold, threshold_max,
		Contours_Callback);
	Contours_Callback(0, 0);

	cv::waitKey(0);
	return 0;
}

void Contours_Callback(int, void*) {
	//二值化
	cv::Mat binary_output;
	cv::threshold(gray_src, binary_output, threshold, threshold * 2,
		cv::THRESH_BINARY);
	//找出轮廓
	vector<vector<cv::Point>> contours;		//多个轮廓的点
	vector<cv::Vec4i> hierarchy;			//轮廓的层次结构
	cv::findContours(binary_output, contours, hierarchy, cv::RETR_TREE,
		cv::CHAIN_APPROX_SIMPLE, cv::Point(-1, -1));

	//找到轮廓后，找轮廓周围的矩形，圆形
	//轮廓周围的多边形
	vector<vector<cv::Point>> contours_poly(contours.size());
	//多边形被矩形包围
	vector<cv::Rect> poly_rects(contours.size());
	//轮廓周围的圆形圆心
	vector<cv::Point2f> ch(contours.size());
	//圆形的半径
	vector<float> radius(contours.size());
	
	//减少轮廓的点数,找出每个轮廓的包围矩形和圆的位置
	for (size_t i = 0; i < contours.size(); ++i) {
		//减少每个轮廓的点数
		cv::approxPolyDP(cv::Mat(contours[i]), contours_poly[i], 3, true);
		//找出被包围的矩形位置
		poly_rects[i] = cv::boundingRect(contours_poly[i]);
		//找出被包围的圆的位置
		cv::minEnclosingCircle(contours_poly[i], ch[i], radius[i]);
	}

	//画出这些矩形和圆
	cv::Mat finalImg;
	src.copyTo(finalImg);
	for (size_t t = 0; t < contours.size(); ++t) {
		//颜色
		cv::Scalar color = cv::Scalar(rng.uniform(0, 255), rng.uniform(0, 255),
			rng.uniform(0, 255));
		//矩形
		cv::rectangle(finalImg, poly_rects[t], color, 2, 8);
		cv::circle(finalImg, ch[t], radius[t], 2, 8);
	}
	cv::imshow("ou", finalImg);

}
```


