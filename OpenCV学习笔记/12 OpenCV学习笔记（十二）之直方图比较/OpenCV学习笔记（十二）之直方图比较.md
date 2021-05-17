## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 直方图比较方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;对输入的两张图像计算得到直方图H1和H2，归一化到相同的尺度空间，然后通过计算H1和H2之间的距离得到两个直方图的相似程度。
## 二. OpenCV提供的比较方法：
1. Correlation - 相关性比较
2. Chi-Square - 卡方比较
3. Intersection - 十字交叉性
4. Bhattacharyya - 巴氏距离

### (一).相关性比较（HISTCOM_CORREL）

<center>
<img src="https://img-blog.csdnimg.cn/20200602215523573.png"   width="70%">

### (二).卡方比较（HISTCOM_CHISQE）

<center>
<img src="https://img-blog.csdnimg.cn/2020060221561165.png"   width="70%">

### (三). 十字交叉性（HISTCOM_INTERSECT）

<center>
<img src="https://img-blog.csdnimg.cn/20200602215659923.png"   width="70%">

### (四). 巴氏距离（HISTCOM_BHATTACHARYYA）

<center>
<img src="https://img-blog.csdnimg.cn/20200602215728536.png"   width="70%">

## 三. API分析

```cpp
cv::compareHist(
	InputArray h1,		//直方图数据
	InputArray h2,
	int method,			//比较方法
)
```

## 四. 代码实现：

```cpp
/*****直方图比较*****/

#include<opencv2/opencv.hpp>
#include<iostream>
#include<math.h>

using namespace std;

string DoubleToString(double b);

int main(int argc, char** argv) {
	cv::Mat src1, src2, src3;			//测试三张图片的相似度
	src1 = cv::imread("1.jpg");
	src2 = cv::imread("10.jpg");
	src3 = cv::imread("10.png");

	//将RGB空间转为HSV空间
	cv::cvtColor(src1, src1, cv::COLOR_BGR2HSV);
	cv::cvtColor(src2, src2, cv::COLOR_BGR2HSV);
	cv::cvtColor(src3, src3, cv::COLOR_BGR2HSV);
	
	//准备直方图的参数
	//两维的直方图
	int h_bins = 50;		//H的直方图的尺寸
	int s_bins = 60;		//S的直方图的尺寸
	int histSize[] = { h_bins, s_bins };

	//直方图的变化范围
	float h_ranges[] = { 0, 180 };	//H的变化范围
	float s_ranges[] = { 0, 256 };	//S的变化范围
	const float* ranges[] = { h_ranges, s_ranges };

	//二维的直方图使用两个通道0， 1
	int channels[] = { 0, 1 };
	//直方图数据
	cv::MatND hist_src1;
	cv::MatND hist_src2;
	cv::MatND hist_src3;

	//生成直方图，并将数据归一化到0-1之间
	cv::calcHist(&src1, 1, channels, cv::Mat(), hist_src1, 2, histSize,
		ranges, true, false);
	cv::calcHist(&src2, 1, channels, cv::Mat(), hist_src2, 2, histSize,
		ranges, true, false);
	cv::calcHist(&src3, 1, channels, cv::Mat(), hist_src3, 2, histSize,
		ranges, true, false);
	//归一化
	cv::normalize(hist_src1, hist_src1, 0, 1, cv::NORM_MINMAX, -1, cv::Mat());
	cv::normalize(hist_src2, hist_src2, 0, 1, cv::NORM_MINMAX, -1, cv::Mat());
	cv::normalize(hist_src3, hist_src3, 0, 1, cv::NORM_MINMAX, -1, cv::Mat());

	//求出直方图的数据后，就可以进行比较了
	double src1_2 = cv::compareHist(hist_src1, hist_src2, cv::HISTCMP_CORREL);
	double src1_3 = cv::compareHist(hist_src1, hist_src3, cv::HISTCMP_CORREL);
	double src2_3 = cv::compareHist(hist_src2, hist_src3, cv::HISTCMP_CORREL);

	cout << "src1 compares with src2 correlation value: " << src1_2 << endl;
	cout << "src1 compares with src3 correlation value: " << src1_3 << endl;
	cout << "src2 compares with src3 correlation value: " << src2_3 << endl;

	//将比较数值显示到图像上
	cv::putText(src1, DoubleToString(src1_2), cv::Point(50, 50),
		cv::FONT_HERSHEY_COMPLEX, 1, cv::Scalar(0, 0, 255), 2, cv::LINE_AA);
	cv::putText(src2, DoubleToString(src2_3), cv::Point(50, 50),
		cv::FONT_HERSHEY_COMPLEX, 1, cv::Scalar(0, 0, 255), 2, cv::LINE_AA);
	
	cv::namedWindow("src1_1", cv::WINDOW_NORMAL);
	cv::namedWindow("src1_2", cv::WINDOW_NORMAL);

	cv::imshow("src1_2", src1);
	cv::imshow("src2_3", src2);

	cv::waitKey(0);
	return 0;

}

//Doublle 转 String
string DoubleToString(double b) {
	ostringstream os;
	if (os << b)
		return os.str();
	return "invalid value";  
}
```



