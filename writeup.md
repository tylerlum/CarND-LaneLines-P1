# **Finding Lane Lines on the Road** 

The goals of this project are to:
* Make a pipeline that finds lane lines on the road using Python and OpenCV
* Write a report that describes the pipeline, identifies potential shortcomings, and suggests possible improvements

---


## 1. Description of the pipeline

### Pipeline Steps

My pipeline consists of 8 steps. 

1. __Grayscale__: Prepare the image for edge detection by converting the image to grayscale.

2. __Gaussian Blur__: Reduce image noise by adding a Gaussian Blur.

3. __Canny Edge Detection__: Detect edges by performing Canny edge detection.

4. __Region of Interest__: Focus on the correct part of the image by defining a region on interest with an image mask.

5. __Hough Transform__: Identify lines in the region of interest using the Hough Transform.

6. __Filter Lines__: Filter out lines that have invalid slope for their location. Invalid slopes include horizontal slopes or slopes with the wrong direction based on the line's location.

7. __Identify A Left And Right Lane Line__: Identify a left and right lane line by performing a weighted average calculation in the rho, theta space.

8. __Draw Lane Lines On Original Image__: Draw the identified lane lines onto the original image through a weighted sum image calculation.

There is an additional optional step between steps 7 and 8 that averages the lane lines identified from this image with the lane lines identified in the last X prevous images, which has the effect of stabilizing lane lines in videos. It does this by storing a "static" variable of a list of past lines, which has a maximum size (ensures that the lane lines still change responsively) and resets automatically after a certain time period (prevents the stored values in a previous video's processing to affect a different video's processing). This effect should only be turned off for different images processed in succession.

### Key Challenges and Design Decisions

In my first iteration, I set very standard values for all of the initial steps. For Step 6, I only filtered out lines that were sloped in the wrong direction, given their location (lines on the left should have negative slope and lines on the right should have positive slope). For Step 7, I performed calculated a simple average of the slopes and intercepts of the lines. 

1. __Grayscale__: This step was very simple and did not require much iteration.

2. __Gaussian Blur__: This step was very simple. I selected a kernel size of (5x5) and I did not see noticeable improvement when changing this size.

3. __Canny Edge Detection__:




In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 


## 2. Potential shortcomings with the pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...

## 3. Possible improvements to the pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
