## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 什么是图像分割？
1. 图像分割的目标是将图像中像素根据一定的规则分为若干个cluster集合，每个集合包含一类像素。
2. 根据算法分为监督学习方法和无监督学习方法 ，图像分割的多数算法都属于无监督学习方法。
## 二. 距离变换常见算法：
1. 不断膨胀/腐蚀
2. 基于倒角距离

## 三. 分水岭变换常见算法：
### (一).基于浸泡理论
#### API
##### 1. 距离变换cv::distanceTransform

```cpp
cv::distanceTransform(
	InputArray src,
	OutputArray dst,
	OutputArray labels,
	int distanceType,
	int maskSize,
	int labelType=DIST_LABEL_CCOMP
)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;**distanceType** = DIST_LI/DIST_L2,
&nbsp;  &nbsp;  &nbsp;  &nbsp;**maskSize**=3 X 3,
&nbsp;  &nbsp;  &nbsp;  &nbsp;**labels**, 离散维诺图输出
&nbsp;  &nbsp;  &nbsp;  &nbsp;**dst**, 输出8位或32位的浮点数，单一通道

##### 2. 分水岭变换cv::watershed

```cpp
cv::watershed(
	InputArray image,
	InputOutputArray markers
)
```

## 四. 代码处理思路：
1. 将白色背景变为黑色，目的是为了后面的变换做准备
2. 使用filter2D与拉普拉斯算子实现图像对比度的提高，sharp
3. 通过threshold转为二值图像
4. 距离变换
5. 对距离变换结果归一化到0-1之间
6. 使用阈值，再次二值化，得到标记
7. 腐蚀得到每个Peak-erode
8. 发现轮廓 - findContours
9. 绘制轮廓 - drawContours
10.分水岭变换 - watershed，并对每个分割区域着色输出

 

```cpp
/*****基于距离变换和分水岭的图像分割*****/

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace std;

int main(int argc, char** argv) {
	cv::Mat src = cv::imread("rec_cir.png");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	//将背景提取，变为黑色
	for (int row = 0; row < src.rows; ++row) {
		for (int col = 0; col < src.cols; ++col) {
			if (src.at<cv::Vec3b>(row, col) == cv::Vec3b(255, 255, 255)) {
				src.at<cv::Vec3b>(row, col)[0] = 0;
				src.at<cv::Vec3b>(row, col)[1] = 0;
				src.at<cv::Vec3b>(row, col)[2] = 0;
			}
		}
	}
	//显示背景变色的图片
	cv::namedWindow("back", cv::WINDOW_NORMAL);
	cv::imshow("back", src);
	
	//锐化
	cv::Mat kernel = (cv::Mat_<float>(3, 3) << 1, 1, 1, 1, -8, 1, 1, 1, 1);
	cv::Mat imgLaplance;			//Laplance变换后的结果
	cv::Mat sharpenImg = src;		//锐化后的结果
	//Laplance变换
	cv::filter2D(src, imgLaplance, CV_32F, kernel, cv::Point(-1, -1),
		0, cv::BORDER_DEFAULT);
	//锐化即为原图减去拉式变换后的图像
	//两个图像相减，图像的类型深度必须相同
	src.convertTo(sharpenImg, CV_32F);
	cv::Mat resultImg = sharpenImg - imgLaplance;

	//图像深度转为8UC3,3通道彩色图像显示
	resultImg.convertTo(resultImg, CV_8UC3);
	imgLaplance.convertTo(imgLaplance, CV_8UC3);
	//显示锐化彩色结果
	cv::namedWindow("sharp", cv::WINDOW_NORMAL);
	cv::imshow("sharp", resultImg);
	src = resultImg;
	//二值化
	cv::Mat binaryImg;
	cv::cvtColor(src, resultImg, cv::COLOR_BGR2GRAY);
	cv::threshold(resultImg, binaryImg, 40, 255, cv::THRESH_BINARY | cv::THRESH_OTSU);
	cv::namedWindow("binary", cv::WINDOW_NORMAL);
	cv::imshow("binary", binaryImg);

	//距离变换
	cv::Mat distImg;
	cv::distanceTransform(binaryImg, distImg, cv::DIST_L1, 3, 5);
	//归一化到0-1
	cv::normalize(distImg, distImg, 0, 1, cv::NORM_MINMAX);
	cv::namedWindow("dist_norm", cv::WINDOW_NORMAL);
	cv::imshow("dist_norm", distImg);

	//距离变换后再二值化
	cv::threshold(distImg, distImg, 0.4, 1, cv::THRESH_BINARY);
	//二值化后腐蚀
	//腐蚀的结构体
	cv::Mat k1 = cv::Mat::ones(3, 3, CV_8UC1);
	cv::erode(distImg, distImg, k1, cv::Point(-1, -1));
	cv::namedWindow("dist_erode", cv::WINDOW_NORMAL);
	cv::imshow("dist_erode", distImg);

	cv::Mat dist_8u;
	//转换为8U深度
	distImg.convertTo(dist_8u, CV_8U);
	//找出轮廓
	vector<vector<cv::Point>> contours;
	cv::findContours(dist_8u, contours, cv::RETR_EXTERNAL, cv::CHAIN_APPROX_SIMPLE,
		cv::Point(0, 0));
	//标记 markerssr
	cv::Mat markers = cv::Mat::zeros(src.size(), CV_32SC1);
	for (size_t i = 0; i < contours.size(); ++i) {
		//在markers上画出轮廓
		cv::drawContours(markers, contours, static_cast<int>(i),
			cv::Scalar::all(static_cast<int>(i)+1), -1);
	}
	cv::circle(markers, cv::Point(5, 6), 3, cv::Scalar(255, 255, 255));
	cv::namedWindow("markers", cv::WINDOW_NORMAL);
	//32S格式的图片显示有问题，需要转换成32F
	//cv::imshow("markers", markers);

	//分水岭算法，
	cv::watershed(src, markers);
	//转化到8UC1深度
	cv::Mat mark = cv::Mat::zeros(markers.size(), CV_8UC1);
	markers.convertTo(mark, CV_8UC1);
	//图像取反
	cv::bitwise_not(mark, mark, cv::Mat());
	cv::namedWindow("watershed image", cv::WINDOW_NORMAL);
	cv::imshow("watershed image", mark);

	//随机着色
	vector<cv::Vec3b> colors;
	for (size_t i = 0; i < contours.size(); ++i) {
		int r = cv::theRNG().uniform(0, 255);
		int g = cv::theRNG().uniform(0, 255);
		int b = cv::theRNG().uniform(0, 255);
		colors.push_back(cv::Vec3b((uchar)b, (uchar)g, (uchar)r));
	}

	//填充每个图像
	cv::Mat dst = cv::Mat::zeros(markers.size(), CV_8UC3);
	//对markers标记进行处理，每个标记的颜色对应着相同的相同数字的索引
	for (int row = 0; row < markers.rows; ++row) {
		for (int col = 0; col < markers.cols; ++col) {
			//markers中每个标记的索引就是对应的颜色
			int index = markers.at<int>(row, col);
			//若标记在所有的标记中
			if (index > 0 && index <= static_cast<int>(contours.size())) {
				//将该标记所标记的区域上色
				dst.at<cv::Vec3b>(row, col) = colors[index - 1];
			}
			//若不是标记的区域，而是背景，上色黑色
			else {
				dst.at<cv::Vec3b>(row, col) = cv::Vec3b(0, 0, 0);
			}
		}
	}
	cv::namedWindow("final", cv::WINDOW_NORMAL);
	cv::imshow("final", dst);

	cv::waitKey(0);
	return 0;
}
```


