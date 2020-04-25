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

![image](https://user-images.githubusercontent.com/26510814/80288619-ebd1fc80-86ed-11ea-9a20-e177708ed83f.png)

_Figure 1: Images showing the lane line finding algorithm in process_

---

There is an additional optional step between steps 7 and 8 that averages the lane lines identified from this image with the lane lines identified in the last X prevous images, which has the effect of stabilizing lane lines in videos. It does this by storing a "static" variable of a list of past lines, which has a maximum size (ensures that the lane lines still change responsively) and resets automatically after a certain time period (prevents the stored values in a previous video's processing to affect a different video's processing). This effect should only be turned off for different images processed in succession.


### Key Challenges and Design Decisions

In my first iteration, I set very standard values for all of the initial steps. For Step 6, I only filtered out lines that were sloped in the wrong direction, given their location (lines on the left should have negative slope and lines on the right should have positive slope). For Step 7, I performed calculated a simple average of the slopes and intercepts of the lines. While this worked reasonably well, there were a few key issues, which are shown below.

![image](https://user-images.githubusercontent.com/26510814/80288680-55eaa180-86ee-11ea-9da1-62667552b255.png)

_Figure 2: The red lines are the filtered lines. The green lines are the extrapolated two lines. It appears that the filtered lines are being identified well, but the extrapolated lane lines did not come out as expected._

---

![image](https://user-images.githubusercontent.com/26510814/80288722-ab26b300-86ee-11ea-8fd4-2a9e347c4fc5.png)

_Figure 3: The red lines are the filtered lines. The green lines are the extrapolated two lines. It appears that the filtered lines are being identified reasonably well, but there are some poorly identified vertical lines and one of the extrapolated lane lines is terribly off._

---

![image](https://user-images.githubusercontent.com/26510814/80288860-c219d500-86ef-11ea-8b9c-d6dd465846c7.png)

_Figure 4: In the challenge video, I got this output which is a warning print statement that I wrote for the edge case in which no lines were found at all. I found that this was happening when the lighting changed. The sunlight made the yellow lines hard to identify._

---

![image](https://user-images.githubusercontent.com/26510814/80288997-859aa900-86f0-11ea-96b1-e9a036f11569.png)

![image](https://user-images.githubusercontent.com/26510814/80289001-95b28880-86f0-11ea-9512-76d15071b838.png)

_Figure 5: Comparison of edgesImage (output from Canny edge detection) without sunlight (top image) and with sunlight (bottom image). Clearly the Canny edge detection thresholds must be modified. The future steps have no edges to work with, so changing their parameters will not improve the line detection in sunlight._

---

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
