## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.API cv::goodFeaturesToTrack

```cpp
cv::goodFeaturesToTrack(
	InputArray image,
	OutputArray corners,					//角点的坐标值
	int maxCorners,							//返回角点的最大数目，返回响应值最强的数目
	double qualityLevel,					//最小可接受的向量值
	double minDistance,					//两个角点之间的最小距离
	InputArray mask=noArray(),
	int blockSize=3,							//计算微分的窗口大小
	bool useHarrisDetector=false,	//是否使用Harris角点检测
	double k = 0.04							//Harris角点检测的参数
)
```
## 二. 下面是代码：

```cpp
/*****Shi-Tomas角点检测*****/

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
}
```


