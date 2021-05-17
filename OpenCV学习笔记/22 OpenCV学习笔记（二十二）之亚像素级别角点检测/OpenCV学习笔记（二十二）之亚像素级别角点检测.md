## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 亚像素级别角点检测
### (一).提高检测精度
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于理论与现实的差别，实际情况下几乎所有的角点不会是一个准确的整数像素点。所以我们需要进行亚像素定位：
1. 插值方法
2. 基于图像矩计算
3. 曲线拟合方法（高斯曲面，多项式，椭圆曲面）

## 二. TermCriteria模板类
&nbsp;  &nbsp;  &nbsp;  &nbsp;先介绍一下TermCriteria模板类，这个类是作为迭代算法的终止条件的。这个类的变量需要3个参数，第一个是**终止条件类型**，第二个参数为迭代的最大次数，最后一个是特定的阈值。
## 三. 终止条件类型：
1. cv::TerCriteria::MAX_ITER     ：迭代终止条件为达到最大迭代次数终止
2. cv::TerCriteria::EPS     ：迭代到阈值终止
3. cv::TerCriteria::MAX_ITER+cv::TerCriteria::EPS     ：两者都作为迭代终止条件

## 四. 亚像素角点检测API 
&nbsp;  &nbsp;  &nbsp;  &nbsp; **cv::cornerSubPix**

```cpp
 cv::cornerSubPix(
        cv::InputArray image, // 输入图像
        cv::InputOutputArray corners, // 角点（既作为输入也作为输出）
        cv::Size winSize, // 区域大小为 NXN; N=(winSize*2+1)
        cv::Size zeroZone, // 类似于winSize，但是总具有较小的范围，Size(-1,-1)表示忽略
        cv::TermCriteria criteria // 结束迭代优化的标准
    );
```

## 五. 代码实现：
&nbsp;  &nbsp;  &nbsp;  &nbsp;**亚像素级别角点检测，再拟合亚像素级别角点位置。**
```cpp
/*****亚像素级别角点检测*****/
/*****先检测出角点，再拟合亚像素级别角点位置*****/

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace std;

int num_corners = 200;
int max_corners = 2000;
cv::Mat src, gray_src;
cv::RNG rng(12345);
void ShiTomasi_Demo(int, void*);
int main(int argc, char** argv) {
	src = cv::imread("建筑.jpg");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	//转灰度图
	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);
	cv::namedWindow("ou", cv::WINDOW_NORMAL);
	cv::createTrackbar("Corner Numbers:", "ou", &num_corners, max_corners,
		ShiTomasi_Demo);
	ShiTomasi_Demo(0, 0);

	cv::waitKey(0);
	return 0;
}

void ShiTomasi_Demo(int, void*) {
	if (num_corners < 5)
		num_corners = 5;

	//角点检测参数
	//存储角点位置
	vector<cv::Point2f> corners;
	//最小可接受的向量值
	double qualityLevel = 0.03;
	//两个角点之间的最小距离
	double minDistance = 10;
	int blockSize = 3;
	bool useHarris = false;
	double k = 0.04;

	cv::Mat resultImg = gray_src.clone();
	cv::cvtColor(resultImg, resultImg, cv::COLOR_GRAY2BGR);
	//角点检测Shi-Tomasi
	cv::goodFeaturesToTrack(gray_src, corners, num_corners, qualityLevel, minDistance,
		cv::Mat(), blockSize, useHarris, k);
	printf("Number of Detected Corners: %d\n", corners.size());

	//画出角点
	for (size_t t = 0; t < corners.size(); ++t) {
		cv::circle(resultImg, corners[t], 10, cv::Scalar(rng.uniform(0, 255),
			rng.uniform(0, 255), rng.uniform(0, 255)), 2, 8, 0);
	}
	cv::imshow("ou", resultImg);

	//拟合亚像素角点位置
	//区域大小
	cv::Size winSize = cv::Size(5, 5);
	//类似于winSize,但总是有较小的范围， （-1，-1）表示忽略
	cv::Size zerozone = cv::Size(-1, -1);
	//迭代循环结束条件，三个参数，1：结束条件，2：最大迭代次数，3：阈值
	//以下TermCriteria表示同时到达最大迭代次数40与角点位置变化的最小值达到0.001则停止迭代
	cv::TermCriteria tc = cv::TermCriteria(cv::TermCriteria::EPS +
		cv::TermCriteria::MAX_ITER, 40, 0.001);
	//计算亚像素角点位置
	cv::cornerSubPix(gray_src, corners, winSize, zerozone, tc);

	for (size_t t = 0; t < corners.size(); ++t) {
		cout << (t + 1) << ".point[x,y]" << corners[t].x <<", "<< corners[t].y << endl;
	}
	return;
}
```

