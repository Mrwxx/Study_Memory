## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 模板匹配
1. 模板匹配就是在整个图像区域匹配到与给定子图像的小块区域
2. 模板匹配首先需要一个模板图像（子图像）
3. 待检测图像
4. 工作方法：在待检测图像上，从左到右，从上到下计算模板图像与待检测图像的匹配度。

## 二. 匹配算法
1. 计算平方不同(cv::TM_SQDIFF)
2. 归一化计算平方不同(cv::TM_SQDIFF_NORMED)
3. 计算相关性(cv::TM_CCORR)
4. 归一化计算相关性(cv::TM_CCORR_NORMED)
5. 计算相关系数(cv::CCOEFF)
6. 归一化计算相关系数(cv:::CCOEFF_NORMED)

## 三. API分析

```cpp
cv::matchTemplate(
	InputArray image;		//原图像， 8-bit或32-bit浮点数图像
	InputArray temp,			//模板图像
	OutputArray result,		//输出结果，必须单通道32位浮点数，假设原图像 W X H，模板图像 w x h, 则结果的大小为 W-w+1， H-h+1
	int method,					//匹配方法
	InputArray mask = noArray()
)
```

## 四. 代码实现：

```cpp
/*****模板匹配*****/

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace std;

cv::Mat src, dst, temp;
int match_method = cv::TM_SQDIFF;	//比较方法
int match_track = 5;				//比较方法数字
void Match_Demo(int, void*);
int main(int argc, char** argv) {
	src = cv::imread("1.jpg");
	temp = cv::imread("2.jpg");		//模板图像
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::namedWindow("temp", cv::WINDOW_NORMAL);
	cv::namedWindow("match", cv::WINDOW_NORMAL);
	cv::imshow("in", src);
	cv::imshow("temp", temp);

	//用拖条改变比较方式
	cv::createTrackbar("Method: ", "match", &match_method, match_track,
		Match_Demo);
	Match_Demo(0, 0);

	cv::waitKey(0);
	return 0;
}

void Match_Demo(int, void*) {
	//改变比较方法
	//准备模板比较参数
	//result  结果尺寸， 32FC1
	int width = src.cols - temp.cols + 1;
	int height = src.rows - temp.rows + 1;
	cv::Mat result(width, height, CV_32FC1);

	//模板匹配
	cv::matchTemplate(src, temp, result, match_method, cv::Mat());
	//归一化 0-1之间
	cv::normalize(result, result, 0, 1, cv::NORM_MINMAX, -1, cv::Mat());

	//在result中找出模板的位置,result中都是数值，根据比较方法分别找出最大值和最小值
	cv::Point minLoc;
	cv::Point maxLoc;
	double min, max;
	cv::Point tempLoc;
	cv::minMaxLoc(result, &min, &max, &minLoc, &maxLoc, cv::Mat());
	//根据比较方法选择最大值，最小值
	if (match_method == cv::TM_SQDIFF || match_method == cv::TM_SQDIFF_NORMED)
		tempLoc = minLoc;
	else
		tempLoc = maxLoc;

	//找到模板所匹配的位置了，绘制矩形显示,矩形大小为模板图像大小
	src.copyTo(dst);
	cv::rectangle(dst, cv::Rect(tempLoc.x, tempLoc.y, temp.cols, temp.rows),
		cv::Scalar(0, 0, 255), 2, 8);
	cv::rectangle(result, cv::Rect(tempLoc.x, tempLoc.y, temp.cols, temp.rows),
		cv::Scalar(0, 0, 255), 2, 8);

	cv::namedWindow("result", cv::WINDOW_NORMAL);
	cv::imshow("result", result);
	cv::imshow("match", dst);
}
```





