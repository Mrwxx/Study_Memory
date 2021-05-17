## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 图像矩
### (一). 几何矩

<center>
<img src="https://img-blog.csdnimg.cn/202006061045203.png"   width="70%">

### (二). 中心矩

<center>
<img src="https://img-blog.csdnimg.cn/20200606104534750.png"   width="70%">

### (三). 中心归一矩

<center>
<img src="https://img-blog.csdnimg.cn/20200606104544740.png"   width="50%">

### (四).  图像中心

<center>
<img src="https://img-blog.csdnimg.cn/20200606104609433.png"   width="50%">

## 二.  API分析
### 1. 计算矩API : cv::moments

```cpp
cv::moments(
	InputArray array,			//输入数据
	bool binaryImage=false //是否为二值图像
)
```
### 2. 轮廓面积API ： cv::contourArea


```cpp
cv::contourArea(
	InputArray contour,		//输入轮廓数据
	bool oriented,				//默认false,返回绝对值
)
```

### 3. 曲线弧长API：cv::arcLength


```cpp
cv::arcLength(
	InputArray curve,			//输入曲线数据
	bool closed					//是否是封闭曲线
)
```

## 三. 代码思路：
1. 提取图像边缘
2. 发现轮廓
3. 计算每个轮廓的矩
4. 计算每个轮廓的中心，面积，弧长

## 四. 代码实现：

```cpp
/*****图像矩*****/

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace std;

cv::Mat src, dst, gray_src;
int threshold = 50;
int threshold_max = 255;
void Moments_Demo(int, void*);
cv::RNG rng(12345);

int main(int argc, char** argv) {
	src = cv::imread("rec_cir.png");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::imshow("in", src);
	cv::namedWindow("ou", cv::WINDOW_NORMAL);

	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);
	//去噪
	cv::GaussianBlur(gray_src, gray_src, cv::Size(3, 3), 0, 0);

	cv::createTrackbar("Threshold Value:", "ou", &threshold, threshold_max,
		Moments_Demo);
	Moments_Demo(0, 0);

	cv::waitKey(0);
	return 0;

}
void Moments_Demo(int, void*) {
	//边缘检测
	cv::Mat Canny_output;
	vector<vector<cv::Point>> contours;
	vector<cv::Vec4i> hierarchy;

	cv::Canny(gray_src, Canny_output, threshold, threshold * 2, 3, false);
	//找出边缘
	cv::findContours(Canny_output, contours, hierarchy, cv::RETR_TREE,
		cv::CHAIN_APPROX_SIMPLE, cv::Point(0, 0));

	//图像矩
	vector<cv::Moments> contours_moments(contours.size());
	//图像的中心
	vector<cv::Point2f> ch(contours.size());
	//计算每个图像的矩
	for (size_t i = 0; i < contours.size(); ++i) {
		//计算图像矩
		contours_moments[i] = cv::moments(contours[i]);
		//图像矩的中心
		ch[i] = cv::Point(static_cast<float>(contours_moments[i].m10 / contours_moments[i].m00),
			static_cast<float>(contours_moments[i].m01 / contours_moments[i].m00));
	}

	//画出图像轮廓，中心,每个轮廓
	cv::Mat finalImg;
	src.copyTo(finalImg);

	for (size_t t = 0; t < contours.size(); ++t) {
		//颜色
		cv::Scalar color = cv::Scalar(rng.uniform(0, 255), rng.uniform(0, 255),
			rng.uniform(0, 255));
		printf("center point x : %.2f y : %.2f\n", ch[t].x, ch[t].y);
		//输出图像的面积，弧度，根据API
		printf("contours %d area : %.2f  arc length : %.2f\n", t, cv::contourArea(contours[t]), cv::arcLength(contours[t], true));
		//画出轮廓
		cv::drawContours(finalImg, contours, t, color, 2, 8, hierarchy,
			0, cv::Point(0, 0));
		cv::circle(finalImg, ch[t], 1, color, 2, 8);
	}

	cv::imshow("ou", finalImg);
	return;

}
```


