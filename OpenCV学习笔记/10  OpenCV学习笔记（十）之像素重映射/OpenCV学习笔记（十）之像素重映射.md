## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">



## 一. 像素重映射
&nbsp;  &nbsp;  &nbsp;  &nbsp;把输入图像中各个像素按照一定的规则映射到另外一张图像的对应位置上。

## 二. API 分析
&nbsp;  &nbsp;  &nbsp;  &nbsp;**cv::remap API分析**

```cpp
cv::remap(
	InputArray src,		//输入图像
	OutputArray dst,	//输出图像
	InputArray map1,//x映射表 	CV_32FC1/CV_32FC2
	InputArray map2,//y映射表
	Int interpolation,	//插值方式
	Int borderMode,	//边界模式
	Const Scalar borderValue //Color
)
```
## 三. 像素重映射代码

```cpp
/*****图像重映射*****/

#include<iostream>
#include<opencv2/opencv.hpp>
using namespace std;

int index = 0;
cv::Mat src, dst;
//x, y 映射表
cv::Mat map_x, map_y;
void update_map();
int main() {
	
	src = cv::imread("1.jpg");
	cv::namedWindow("in_image", cv::WINDOW_NORMAL);
	cv::imshow("in_image", src);

	//映射表，单通道
	map_x.create(src.size(), CV_32FC1);
	map_y.create(src.size(), CV_32FC1);

	int c = 0;
	while (true) {
		c = cv::waitKey(500);
		if ((char)c == 27)
			break;
		index = c % 4;
		update_map();
		cv::remap(src, dst, map_x, map_y, cv::INTER_LINEAR,
			cv::BORDER_CONSTANT, cv::Scalar(0, 0, 255));
		cv::namedWindow("ou_image", cv::WINDOW_NORMAL);
		cv::imshow("ou_image", dst);
	}
	return 0;

}

void update_map() {
	//更改映射表
	for (int row = 0; row < src.rows; ++row) {
		for (int col = 0; col < src.cols; ++col) {
			switch (index)
			{
			//缩小一半的图像
			case 0:
				if (col > (src.cols * 0.25) && col < (src.cols * 0.75)
					&& row >(src.rows * 0.25) && row < (src.rows * 0.75)) {
					map_x.at<float>(row, col) = 2 * (col - src.cols * 0.25);
					map_y.at<float>(row, col) = 2 * (row - src.rows * 0.25);
				}
				//除中心位置外为黑色
				else {
					map_x.at<float>(row, col) = 0;
					map_x.at<float>(row, col) = 0;
				}
			//左右颠倒
			case 1:
				map_x.at<float>(row, col) = src.cols - col - 1;
				map_x.at<float>(row, col) = row;
			//上下颠倒
			case 2:
				map_x.at<float>(row, col) = col;
				map_y.at<float>(row, col) = src.rows - row - 1;
			default:
				break;
			}
		}
	}
}
```



