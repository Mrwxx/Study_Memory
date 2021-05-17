## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 自定义Harris角点检测器
1. 基于Harris角点检测
2. 首先通过计算矩阵M得到两个特征值，然后计算得到角点响应值
3. 设置阈值，通过阈值计算得到有效响应的角点位置

##  二. API分析 
&nbsp;  &nbsp;  &nbsp;  &nbsp;  **cv::cornerEigenValsAndVecs**
&nbsp;  &nbsp;  &nbsp;  &nbsp;**自定义角点检测器**
```cpp
cv::cornerEigenValsAndVecs(
	InputArray src,
	OutputArray dst,
	int blockSize,
	int kSize,
	int borderType=BORDER_DEFAULT
)
```
## 二. 代码实现：

```cpp
/*****自定义Harris角点检测器*****/

#include<opencv2/opencv.hpp>
#include<iostream>
#include<math.h>
using namespace std;

cv::Mat src, gray_src;
//最小Harris响应
double harris_min_rsp;
double harris_max_rsp;

int qualityLevel = 30;
int max_count = 100;
//Harris结果
cv::Mat harris_dst;
//每个像素响应的值存储
cv::Mat harrisRspImg;
void CumstomHarris_Demo(int, void*);

int main(int argc, char** argv) {
	src = cv::imread("建筑.jpg");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);
	//计算特征值
	int blockSize = 3;
	int kSize = 3;
	double k = 0.04;

	//3个通道，每个通道两个值
	harris_dst = cv::Mat::zeros(src.size(), CV_32FC(6));
	//角点计算
	cv::cornerEigenValsAndVecs(gray_src, harris_dst, blockSize, kSize, 4);

	//计算响应

	harrisRspImg = cv::Mat::zeros(src.size(), CV_32FC1);
	for (int row = 0; row < harris_dst.rows; ++row) {
		for (int col = 0; col < harris_dst.cols; ++col) {
			double lambda1 = harris_dst.at<cv::Vec6f>(row, col)[0];
			double lambda2 = harris_dst.at<cv::Vec6f>(row, col)[1];
			//公式计算响应值
			harrisRspImg.at<float>(row, col) = lambda1 * lambda2 - k * pow((lambda1 + lambda2), 2);
		}
	}
	//查找最小值，最大值的位置
	cv::minMaxLoc(harrisRspImg, &harris_min_rsp, &harris_max_rsp, 0, 0, cv::Mat());

	cv::namedWindow("ou", cv::WINDOW_NORMAL);
	cv::createTrackbar("Quality Value:", "ou", &qualityLevel, max_count,
		CumstomHarris_Demo);
	CumstomHarris_Demo(0, 0);

	cv::waitKey(0);
	return 0;
}

void CumstomHarris_Demo(int, void*) {
	if (qualityLevel < 10)
		qualityLevel = 10;

	cv::Mat resultImg = src.clone();
	//响应值的阈值
	float t = harris_min_rsp + (((double)qualityLevel) / max_count) *
		(harris_max_rsp - harris_min_rsp);
	for (int row = 0; row < src.rows; ++row) {
		for (int col = 0; col < src.cols; ++col) {
			//响应值只有大于阈值才是角点
			float v = harrisRspImg.at<float>(row, col);
			if (v > t) {
				//画出角点，关键，col,row是反过来的，因为图像的横坐标是图像的列，纵坐标是图像的行
				circle(resultImg, cv::Point(col, row), 2, cv::Scalar(0, 0, 255),
					2, 8, 0);
			}
		}
	}
	cv::imshow("ou", resultImg);
}
```

