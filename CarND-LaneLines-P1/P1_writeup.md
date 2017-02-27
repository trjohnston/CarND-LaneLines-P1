#**Finding Lane Lines on the Road** 

##Writeup Template

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 7 principal steps (i.e., funciton calls). Specifically, the functions calls were name: 
* rgb_filter
* grayscale
* gaussian_blur
* canny
* region_of_interest
* hough_lines
* weighted_lines

White and lane stripes are defined by the Federal Standard Color and have an RGB value of [247, 241, 227] and [255, 183, 0], respectively [[1]](http://www.sciencedirect.com/science/article/pii/S0957417414006484). 
The rgb_filter sets pixel intesity thresholds that preferentially identify the white and yellow lane lines and "blacks out" all pixels that fall below these thresholds. Unsurprisingly, the grayscale function converts the rgb_filtered image to grayscale. The grayscale image is then passed through a Gaussian blur filter which averages neighboring pixels and creates "smoother" edges. Once smoothed, the Canny edge detection finds edges by examining the gradient of neighboring image intensities. Since many of the detected images do not correspond to portions of the image that are of interest, pixels outside of the region of interest are set to black. Next, edges within our region of interest are passed through a Hough transform.  The Hough transform edges that make up a common line. 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by separating the hough lines into two goups, one having positive slopes and the other negative slopes. The algorithm rejects all lines whose slope did not fall within a narrow range of interest. The remaining lines in each group were then averaged both in terms of slope and resultant center point. With the slope and one (x, y) coordinates of the center point known, the averaged line was then extrapolated to the bottom of the image and up towards the upper bounds of the region of interest.

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


###2. Identify potential shortcomings with your current pipeline


The pipeline described above is very simple and prone to failure under non-ideal conditions. One of the major limitaitons is illumination. In the current implementation, the algorithm is very sensitive shadows and low-light levels. A second limitation is how the region of interest is defined, i.e., as a fixed area. This method does not work well for hilly or very curvy roads. The weighted lines function is also not very robust to curvy roads. Finally, the pipeline is not optimized for speed and would not work well in a safety-critical application.


###3. Suggest possible improvements to your pipeline

Robustness to illumination could be achieved by converting the color images to the YCrCb color space. According to [1], regardless of illumination, white pixels will have the highest intensity values and appropriate color thresholds can be found for the Y (luminance) channel. Similarly, yellow pixels always have the lowest intensity values in the Cb channel.

The region of interest mask could be made "dynamic" using vanishing points and the horizon to more intelligently separate the image. 

There are several options to improve the weighted hough lines. First, we could retain several sequential image frames and create a "moving average". We could also use techniques to predict road curvature based on a polynomial (or other non-linear) function. 

