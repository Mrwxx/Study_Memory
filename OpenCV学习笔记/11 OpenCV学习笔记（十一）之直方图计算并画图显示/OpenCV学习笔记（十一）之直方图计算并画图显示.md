## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 直方图
&nbsp;  &nbsp;  &nbsp;  &nbsp;对于图像梯度，每个像素的角度，等图像的属性都可以建立直方图。
## 二. 直方图的常见属性：
1. **dims**-表示维度
2. **bins**-表示维度中子区域大小划分,bins=256,表示划分为256个级别
3. **range**-表示值的范围，灰度值范围为[0,255]

## 三. API 分析
1. **cv::split ()把多通道图像分为多个单通道图像**

```cpp
split(			
	const Mat & src,			//输入图像
	Mat* mvbegin				//输出的通道图像数组
）
```

2. **cv::calcHist()生成直方图数据**

```cpp
calcHist(
	const Mat* images,		//输入图像指针
	int nimages,					//图像数目
	const int* channels,		//通道数
	InputArray mask,			//掩膜
	OutputArray hist,			//直方图数据
	int dims,						//维数
	const int* histSize,		//直方图级数
	const float* ranges,		//值域范围
	bool uniform,				
	bool accumulate
)
```
## 四. **代码实现**

```cpp
/*****直方图计算*****/

#include<opencv2/opencv.hpp>
#include<vector>
using namespace std;
int main() {
	cv::Mat src, dst;
	src = cv::imread("1.jpg");
	cv::namedWindow("in", cv::WINDOW_NORMAL);
	cv::imshow("in", src);

	//将多通道图像分为单通道图像
	//单通道图像vector
	vector<cv::Mat>  bgr_planes;
	cv::split(src, bgr_planes);

	//直方图参数
	int histSize = 256;
	float range[] = { 0, 256 };
	const float* histRanges = { range };
	cv::Mat b_hist, g_hist, r_hist;
	//求出直方图的数据
	cv::calcHist( &bgr_planes[0], 1, 0, cv::Mat(), b_hist, 1, &histSize,
		&histRanges, true, false);
	cv::calcHist(&bgr_planes[1], 1, 0, cv::Mat(), g_hist, 1, &histSize,
		&histRanges, true, false);
	cv::calcHist(&bgr_planes[2], 1, 0, cv::Mat(), r_hist, 1, &histSize,
		&histRanges, true, false);

	//画出直方图
	int hist_h = 400;
	int hist_w = 512;
	int bin_w = hist_w / histSize;		//直方图的步数
	cv::Mat hist_image(hist_w, hist_h, CV_8UC3, cv::Scalar(0, 0, 0));
	//将数据归一化到直方图的图像尺寸中来
	cv::normalize(b_hist, b_hist, 0, hist_h, cv::NORM_MINMAX, -1, cv::Mat());
	cv::normalize(g_hist, g_hist, 0, hist_h, cv::NORM_MINMAX, -1, cv::Mat());
	cv::normalize(r_hist, r_hist, 0, hist_h, cv::NORM_MINMAX, -1, cv::Mat());

	//0-255 的像素值，画出每个像素值的连线
	//注意： 图像中的坐标是以左上角为原点向右下方延伸
	for (int i = 1; i <histSize; ++i) {
		cv::line(hist_image, cv::Point((i - 1) * bin_w, hist_h - cvRound(b_hist.at<float>(i - 1))),
			cv::Point(i * bin_w, hist_h - cvRound(b_hist.at<float>(i))), cv::Scalar(0, 0, 255), 2, cv::LINE_AA);
		cv::line(hist_image, cv::Point((i - 1) * bin_w, hist_h - cvRound(g_hist.at<float>(i - 1))),
			cv::Point(i * bin_w, hist_h - cvRound(g_hist.at<float>(i))), cv::Scalar(0, 255, 0),2, cv::LINE_AA);
		cv::line(hist_image, cv::Point((i - 1) * bin_w, hist_h - cvRound(r_hist.at<float>(i - 1))),
			cv::Point(i * bin_w, hist_h - cvRound(r_hist.at<float>(i))), cv::Scalar(255, 0, 0), 2, cv::LINE_AA);
	}
	cv::namedWindow("ou", cv::WINDOW_NORMAL);
	cv::imshow("ou", hist_image);

	cv::waitKey(0);
	return 0;
}
```


