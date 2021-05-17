## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 查找轮廓
&nbsp;  &nbsp;  &nbsp;  &nbsp;查找轮廓是在图像边缘提取的基础上，寻找对象的轮廓的方法。所以边缘提取的阈值选定会影响最终轮廓发现的结果。
## 二. API分析
### 1. cv::findContours

```cpp
cv::findCountours(
	InputArray binImg;		//输入图像
	OutputArrayOfArrays contours,	//发现的轮廓对象
	OutputArray hierarchy,//图像的拓扑结构
	int mode,						//轮廓返回的模式
	int method,					//发现的方法
	Point offset= Point()	//轮廓像素的位移
)	
```

### 2. cv::drawContours

```cpp
cv:drawContours(
	InputOutputArray binImg;	//输入图像
	OutputArrayOfArrays contours,	//轮廓对象
	Int contourIdx,						//轮廓索引号
	const Scalar & color,			//绘制的颜色
	int thickness,						//绘制线宽
	int lineType,							//绘制线性
	InputArray hierarchy,			//拓扑结构图
	int maxlevel,						//最大层数，0为只绘制当前的，1为绘制当前及内嵌的轮廓
	Point offset = Point()			//轮廓位移
)
```

## 三. 代码流程：
1. 输入图像转为灰度图像--cvtColor
2. 使用Canny进行边缘提取，得到二值图像
3. 使用findContours寻找轮廓
4. 使用drawContours绘制轮廓

## 四. 代码实现
```cpp
/*****轮廓查找*****/

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace std;

int threshold = 50;
int threshold_max = 150;
cv::Mat src, dst, gray_src;

void Demo_Contours(int, void*);
int main(int argc, char** argv) {
	src = cv::imread("1.jpg");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::namedWindow("ou", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	//转灰度图像
	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);
	
	//Canny边缘检测
	cv::createTrackbar("Canny Threshold: ", "ou", &threshold, threshold_max,
		Demo_Contours);
	Demo_Contours(0, 0);

	cv::waitKey(0);
	return 0;
}

void Demo_Contours(int, void*) {
	//Canny边缘检测
	cv::Mat canny_edge;			//Canny输出edge
	cv::Canny(gray_src, canny_edge, threshold, threshold * 2, 3, false);

	//轮廓点的vector
	vector<vector<cv::Point>> contours;
	//轮廓的拓扑图,即轮廓的结构层次
	vector<cv::Vec4i> hierarchy;
	//通过canny边缘检测的结果查找轮廓
	cv::findContours(canny_edge, contours, hierarchy, cv::RETR_TREE,
		cv::CHAIN_APPROX_SIMPLE, cv::Point(0, 0));

	//得到轮廓点后准备画出所有的轮廓
	dst = cv::Mat::zeros(src.size(), CV_8UC3);
	//随机数
	cv::RNG rng(12345);
	for (size_t i = 0; i < contours.size(); ++i) {
		cv::Scalar color = cv::Scalar(rng.uniform(0, 255), rng.uniform(0, 255),
			rng.uniform(0, 255));
		cv::drawContours(dst, contours, i, color, 2, 8, hierarchy,
			0, cv::Point());
	}

	cv::imshow("ou", dst);

}
```

