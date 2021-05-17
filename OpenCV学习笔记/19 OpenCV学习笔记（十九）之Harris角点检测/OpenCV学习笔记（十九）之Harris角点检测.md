## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. Harris角点检测
### API cv::cornerHarris

```cpp
cv::cornerHarris(
	InputArray src,			//灰度图像
	OutputArray dst,		//响应输出，可能为小数
	int blockSize,			//矩阵大小
	int ksize,					//窗口大小
	double k,					//计算角度响应时的参数大小，默认在0.04-0.06
	int borderType=BORDER_DEFAULT
)
```

## 二. 代码实现：

```cpp
/*****Harris角点检测*****/

#include<opencv2/opencv.hpp>
#include<iostream>
cv::Mat src, gray_src;
int thresh = 50;
int thresh_max = 255;
void Harris_Demo(int, void*);
using namespace std;
int main(int argc, char** argv) {
	
	src = cv::imread("建筑.jpg");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	cv::namedWindow("ou", cv::WINDOW_NORMAL);
	//转灰度图
	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);
	
	cv::createTrackbar("Threshold Value: ", "ou", &thresh, thresh_max,
		Harris_Demo);
	Harris_Demo(0, 0);

	cv::waitKey(0);
	return 0;
}

void Harris_Demo(int, void*) {
	cv::Mat dst, norm_dst, resultImg, normScaleDst;
	dst = cv::Mat::zeros(gray_src.size(), CV_32FC1);

	//角点检测参数准备
	int blockSize = 2;
	int kSize = 3;
	double k = 0.04;
	cv::cornerHarris(gray_src, dst, blockSize, kSize, k, cv::BORDER_DEFAULT);

	//灰度图归一化到0-255
	cv::normalize(dst, norm_dst, 0, 255, cv::NORM_MINMAX, CV_32FC1, cv::Mat());
	//绝对值
	cv::convertScaleAbs(norm_dst, normScaleDst);

	//在原图上显示角点的位置
	resultImg = src.clone();
	for (int row = 0; row < resultImg.rows; ++row) {
		//用行指针
		uchar* currentRow = normScaleDst.ptr(row);
		for (int col = 0; col < resultImg.cols; ++col) {
			//像素值
			int value = (int)*currentRow;
			//像素值小于阈值
			if (value > thresh) {
				//在角点画个小圆
				circle(resultImg, cv::Point(row, col), 2, cv::Scalar(0, 0, 255),
					2, 8, 0);
			}
			currentRow++;
		}
	}
	cv::imshow("ou", resultImg);

}
```


