# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[5-final]: ./examples/solidYellowLeft.jpg "solidYellowLeft.jpg - Final"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale and applied Gaussian filter with Kernel size of 5. Then I applied Canny Edge detector with thresholds 50 and 150. I then applied a region mask to use only the bottom part of the image. I applied Hough transform and some filtering to detect possible lane markings. Finally, I created a weighted image to overlay the lane lines on the original image.

Here is the result of the pipeline applied to one of the sample images (solidYellowLeft.jpg).

![solidYellowLeft.jpg - Final][5-final]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by first discarding all lines that have `slope = 0` or those that have the y-intercept less than the height of the image, eg. in case of barriers along lane markings. Then the lines are categorized into those having positive or negative slopes to represent either sides of the lanes. I then computed the average value of the lower-most and upper-most points for both lanes. I store these averages in a moving window of last 20 frames, and take the median for slope and y-intercept to draw the final lines. Due to median filtering, the lines don't jump abruptly. If a lane marking is not detected in a frame, I discard the oldest entry in my moving window so that the values are from last 20 frames.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming that is also seen when the pipeline is applied to the challenge image is when there is a ramp along the edge of the road. This could be wrongly identified as the lane or the coordinates would skew the real lane markings. I have mitigated this to some extent by discarding lines based on the slope and y-intercept, but this is not so robust. This behavior could also be observed in real world when there are temporary (yellow markings in EU roads) lane markings on the road due to construction work. These temporary lane markings should take precedence over the permanent (white markings in EU roads) markings.

Another shortcoming could be that the lane detection would be a bit slow to adapt to sharp turns due to median filtering.

My pipeline expects that the vehicle starts already within two lane markings. This might not always be the case, eg. when it is driving out of a driveway to the road.

Furthermore, as it can be observed from the challenge video, the pipeline is not robuts to changing lighting condition.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use clustering algorigthm to see if there are indeed more than one line detected, one of which could be a ramp or a temporary marking. Then, based on the expected distance between the lanes or some other paramter, the correct edge could be considered.

Another potential improvement could be to use regression instead of averaging the points detected by Hough transform. Using a higher order polynomial function for regression, we could even account for curved paths.

As for the assumption that the vehicle always has to start from within the lane markings, I hope further lectures in the Nano degree program would address this problem.
