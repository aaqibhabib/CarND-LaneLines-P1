# **Finding Lane Lines on the Road** 

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[lane-markings]: ./lane-markings.PNG "Lane Markings"
[region-of-interest]: ./area-of-interest.PNG "Area of Interest"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I blurred the image using a 7 pixel kernel, using the gaussian blur function. Next, I used the canny edge detector to find the prominent lines. The upper and lower thresholds were found by trial and error until most of the road lines were outlined. I didn't spend too much time getting this perfect, ie: missing a few of the smaller edges that are road marking, but I did aim to capture at least 1 near/large dashed line and 1 far/small dashed line for each of the test images.
Following this, I created a trapezoid that defined my region of interest. This was hand-tuned to capture the current paths right and left road lines, spanning from the bottom of the image to about 1/3 up from the bottom. See figure below.

![Region on Interest][region-of-interest]

After that, I used the Hoffman Transform to compute where the detected lines are. I tuned the input parameter until the large line and close dashed lines would be selected.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by first bucketing the Hoffman lines into those with positive and negative slopes. The positive sloped were part of the left lane and the negative slope were for the right lane. Visually, this is reversed because the origin of the image in the top-left corner and the bottom-right corner is in the positive axis direction. Then I computed the median slope and median y-intercept for each of the lines, which would be a close approximation of the true lane. The median value removed any influence from stray detection reasonably well. Using these values, I drew the corresponding lines extending from the bottom to about 1/3 of the way up. See figure below for the result.


![Lane Markings][lane-markings]


### 2. Identify potential shortcomings with your current pipeline


My pipleline assumes at least 1 detection will be made for each of the right and left lanes. My parameters happen to detect them in the examples provided, but it's unlikely this will be always the case. The code should be extending to support 0 detections for either of the lines.

Another shortcoming is with the parameters chosen for each of the filters. The blur function, Canny detection, and Hoffman Transform functions are all tuned for the well-lit images/videos as part of the example. As seen in the challenge video, low-light scenes result in significant error.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to implement a smoothing function for the lane slope and y-intercept. Using something like a 10 frame moving average for each would yield a smoother result. 

Another potential improvement could be to filter the image to pick yellow and white elements, the color of lane markings, and then grayscale the image. The goal would be to amplify what could possibly be a lane and minimize what definitely doesn't appear to be a lane marking.
