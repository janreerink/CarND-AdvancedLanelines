## Writeup
---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undist_before_after.jpg "Undistorted"
[image2]: ./examples/undist_before_after2.jpg "Undistorted road"
[image3]: ./examples/threshold_before_after.jpg "Binary Example"
[image4]: ./examples/warp_before_after.jpg "Warp Example"
[image5]: ./examples/lane_fitting.jpg "Fit Visual"
[image6]: ./examples/result.jpg "Output"
[video1]: ./p_output_lanelines.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

The submission comes with two Jupyter notebooks. The first notebook (advanced lanefinding) generates the sample images shown here. The second (advanced lanefinding video) contains the pipeline for creating the video.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The first notebook ("./advanced lane finding.ipynb") starts with camera calibration. As shown in the lessons, the supplied calibration images are used to generate the camera matrix and distortion matrix 
after the corner points on the chess-board patterns are identified and used together with the object points. The matrix and coefficients are then used to remove the distortion effects on one of the provided images.

Here is the image before and after correction:
![alt text][image1]


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The matrix and coefficients are then applied to the provided test images of lanes, here is an example:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Cell number 5 shows the combination of color and gradient thresholding. A combination of the Sobel gradient in x-direction and the S-channel of the HSV color space with suitable thresholds returned good results,
as shown here:
![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The next cells show the perspective transform on test pictures. The input coordinates and the corresponding square to transform to a top-down view of the lane were eyeballed using a trapezoid shape
along the lane lines. The following image shows that the lane lines appear approximately parallel once transformed:
![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I used the historgram-based lane detection as suggested in the lessons to identify lane pixels and fit a second order polynomial. The histogram detects a starting point for a sliding
window search which filters out areas with low pixel densities. Once the lanes are fitted the sliding window search can be modified to look for lane pixels only in the area surrounding the last fitted polynomial.
 
![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The next cells calculate the curvature in pixels and meters as shown in class. The result is printed in the respective notebook cells.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The fitted point coordinates are converted into a shape usable by CV2 and used to draw a polygon on a blank, warped image. Using the inverted matrix obtained from the perspective transformation
this picture is turned back into the original perspective and used as overlay on the (undistorted) input picture. The result is shown here for one test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

The second notebook contains the pipeline as applied to the video and includes some sanity checks (tests if lines are parallel, not too distant and have similar curvature) and smoothing (average of last several fits is used to draw the lane) in order to prevent bad 
results from distorting the lane lines too much. If the detected lines are not good a new sliding window search is initiated, otherwise the shorter update function is used to look in the vicinity of the last fit.
The notebook also contains a region of interest masking as in the first lane line project. 
The video is annotated with info on lane curvature radius and distance of detected lane center to image center (as estimate for position of vehicle on road).

Here's a [link to my video result](./p_output_lanelines.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline described above works reasonably well for the project video (with some minor glitches). It does not work well for the challenge videos. More fine-tuning for the color and gradient thresholding is 
required to prevent parts of the road with different colors, shadows and other cars from interfering with the result.
The even harder challenge video shows that the pipeline relies on some assumptions such as roads with little inclination and not a lot of distration from other traffic as well as somewhat consistent lighting conditions.
After feedback from the reviewer modifications to the color selection turned out to be helpful for some problematic areas of the project video.
