# **Finding Lane Lines on the Road** 

The goals of this project are to:
* Make a pipeline that finds lane lines on the road using Python and OpenCV
* Write a report that describes the pipeline, identifies potential shortcomings, and suggests possible improvements

## Examples of the code in action

![Alt Text](files_for_documents/solidWhiteRight.gif)

_Figure 1: Lane line detection is very accurate for simple scenarios_

---

![Alt Text](files_for_documents/challenge.gif)

_Figure 2: Lane line detection still works reasonably well in challenging scenarios that include turns and changing lighting_

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

_Figure 3: Images showing the lane line finding algorithm in process_

---

There is an additional optional step between steps 7 and 8 that averages the lane lines identified from this image with the lane lines identified in the last X prevous images, which has the effect of stabilizing lane lines in videos. It does this by storing a "static" variable of a list of past lines, which has a maximum size (ensures that the lane lines still change responsively) and resets automatically after a certain time period (prevents the stored values in a previous video's processing to affect a different video's processing). This effect should only be turned off when images from entirely different scenarios are processed in quick succession.


### Key Challenges and Design Decisions

In my first iteration, I set very standard values for all of the initial steps. For Step 6, I only filtered out lines that were sloped in the wrong direction, given their location (lines on the left should have negative slope and lines on the right should have positive slope). For Step 7, I calculated a simple average of the slopes and intercepts of the lines. While this worked reasonably well, there were a few key issues, which are shown below.

__Issue 1: Slightly inaccurate extrapolation from filtered lines to two lane lines__

![image](https://user-images.githubusercontent.com/26510814/80288680-55eaa180-86ee-11ea-9da1-62667552b255.png)

_Figure 4: The red lines are the filtered lines. The green lines are the extrapolated two lines. It appears that the filtered lines are being identified well, but the extrapolated lane lines did not come out as expected._

---

__Issue 2: Very poor extrapolation from filtered lines to two lane lines__

![image](https://user-images.githubusercontent.com/26510814/80288722-ab26b300-86ee-11ea-8fd4-2a9e347c4fc5.png)

_Figure 5: The red lines are the filtered lines. The green lines are the extrapolated two lines. It appears that the filtered lines are being identified reasonably well, but there are some poorly identified vertical lines and one of the extrapolated lane lines is terribly off._

---

__Issue 3: Inability to detect lane lines in sunlight__

![image](https://user-images.githubusercontent.com/26510814/80288860-c219d500-86ef-11ea-8b9c-d6dd465846c7.png)

_Figure 6: In the challenge video, I got this output which is a warning print statement that I wrote for the edge case in which no lines were found at all. I found that this was happening when the lighting changed. The sunlight made the yellow lines hard to identify._

---

![image](https://user-images.githubusercontent.com/26510814/80288997-859aa900-86f0-11ea-96b1-e9a036f11569.png)

![image](https://user-images.githubusercontent.com/26510814/80289001-95b28880-86f0-11ea-9512-76d15071b838.png)

![image](https://user-images.githubusercontent.com/26510814/80290301-53417980-86f9-11ea-801b-47655f4c8a10.png)

_Figure 7: Comparison of edgesImage (output from Canny edge detection) without sunlight (top image) and with sunlight (middle image). The bottom image shows what the sunlight condition looks like. Clearly the Canny edge detection thresholds must be modified. The future steps have no edges to work with, so changing their parameters will not improve the line detection in sunlight._

---

From these symptoms, I identified these solutions:

* __Solution to Issue 1__: To resolve the issue in which filtered lines looked accurate, but the extrapolated two lines were slightly off, I changed the method of averaging. First, I made this a weighted average calculation, so each line's contribution to the average is scaled by its length. This ensures that long lines make a larger contribution to the extrapolation than short ones. This resolved issue 1 shown in Figure 4 by improving the accuracy of the extrapolated lines when they were slightly off. However, this did not fix issue 2 shown in Figure 5, where the extrapolated line is totally off. 

* __Solution to Issue 2__: I realized this issue was related to the nearly vertical lines, as they would have incredibly large slope and interept values, which would completely throw off the weighted average. I resolved this issue by changing the state space in which the averaging was performed. I converted each line to its (rho, theta) representation and then performed the weighted average calculation on these values, since their range is much smaller and the weighted average calculation would be more stable. At the end, I would convert the averaged rho and theta back to slope and intercept, which resolved issue 2 shown in Figure 5.

* __Solution to Issue 3__: To resolve the issue in which sunlight ruined the edge detection, I modified the Canny edge detection thresholds to be able to detect lines, even in these lighting conditions. However, this made the algorithm more susceptible to noise, as it would find irrelevant lines in the center that threw off the average. Thus, I modified the region of interest from its original trapezoid by removing a triangle shaped region in the center. This way, I would still be looking in a reasonably large region of interest to be able to see lane lines of different curvatures, but not be affected by noise in the middle of the road. I also added a "static" variable that stored the most recent lines found. If there were ever no lines found, it would still print out the warning message, but rather than return complete dummy values, it would return the most recent lines.

* Lastly, I added the ability to average the lines between different images in a video to stabilize the lines. This would also help for scenarios in which lane lines are temporarily obstructed by obstacles such as cars or poor lighting, so that we could not identify the lane lines from those images alone, but we can estimate where the lane lines would be from previous images.

## 2. Potential shortcomings with the pipeline

Some potential shortcomings of the pipeline include:

* __Limited region of interest__: As of right now, the region of interest is a trapezoid with a center triangle removed. This works very well for forward-facing cameras on cars and for situations in which the car is simply staying in the middle of the lane. However, this would not work well for cameras that look side-ways or diagonally, or if they are tilted upwards or downwards.

* __Potential sensitivity to lighting__: As of right now, the parameters are set to work very well in well-lit conditions like those shown in the test images and videos. However, in situations with very, very high brightness (sun shining directly at the camera) or low-brightness (lighting from moon, streetlights, or headlights), this may not be able to detect lines properly.

* __Inability to draw curved lines__: As of right now, the lane lines detected are always two straight lines. While this works well for straight roads or slightly curved roads, this may not work for roads with sharper turns, such as round-abouts.

## 3. Possible improvements to the pipeline

* __Expand region of interest__: We could expand the region of interest to better identify lane lines in all situations. This would need to be coupled with additional improvements below to avoid issues of robustness.

* __Improving filtering algorithm__: The filtering algorithm could be improved to not simply remove lines with a certain slope range, but to use graph algorithms to identify clusters and outliers. The clusters can be merged together into single lines and the outliers can be removed.

* __Improving line merging algorithm__: The line merging algorithm currently uses a weighted average of the (rho, theta) of the lines. It also averages across frames to reduce shakiness. However, this could be improved by more robust algorithms, such as using a median filter and removing outliers.

* __Changing parameters based on situation__: Rather than rely on hard-coded parameters for edge and line detection, we could have these parameters change automatically based on situations such as lighting conditions, weather, speed, etc.

* __Allow for curved lines__:  We could create higher order polynomials to bring the lines together, rather than simple straight lines.


## Conclusion

This projects demonstrates the effectiveness of a computer vision solution to lane line detection. In particular, it shows that the use of Canny edge detction, the Hough transform, and weighted averaging techniques can reliably detect lane lines for simple driving scenarios.
