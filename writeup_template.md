## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[//]: # (Examples images)

[image1]: ./output_images/undistort.png "Undistorted chess"
[image2]: ./output_images/undcar.jpg "Undistorted"
[image3]: ./output_images/Unwarped.jpg "Road Transformed"
[image4]: ./output_images/result.jpg "Result"
[image8]: ./output_images/pipeline.jpg "pipeline for all images"
[image9]: ./output_images/RGB2Lab.jpg "RGB2Lab"
[image10]: ./output_images/hls_l.jpg "hls_l"
[image11]: ./output_images/gradient.jpg "gradient"
[image12]: .output_images/DIR.jpg "dir"
[image13]: ./output_images/Mag.jpg "mag"
[image14]: ./output_images/sobel.jpg "sobel"
[image15]: ./output_images/find_lines.jpg "find_lines"
[video1]: ./output_video/project_video_output.mp4 "Video"
[video2]: ./output_video/challenge_video.mp4 "Challenge video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./advanced-lane-finding.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (see in "./advanced-lane-finding.ipynb".).

I used many variant of thresholds:
* abs_sobel_thresh
![alt text][image14]

* Magnitude of the Gradient
![alt text][image13]

* Direction of the Gradient
![alt text][image12]

* combine all gradient method
![alt text][image11]

* HLS Color Threshold
![alt text][image10]

* RGB2Lab
for searching yellow line
![alt text][image9]

I got a bad result for gradient threshold.
I got a good result on a combination HLS (L channel) and RGB2Lab threshold in function pipeline.

![alt text][image8]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `unwarp`, which appears in "./advanced-lane-finding.ipynb":


| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image3]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In "./advanced-lane-finding.ipynb" i write function search_around_poly, based on course materials. The main difference is the larger return parameters and the definition when we could not find the lines.

![alt text][image15]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in `./advanced-lane-finding.ipynb` in function `curvature_center`. We calculate `left_lane_p` (left_fit_cr[0]*(y_eval_m)**2 + left_fit_cr[1]*(y_eval_m) + left_fit_cr[2]) and `right_lane_p` and position of vechicle as (left_lane_p + right_lane_p)/2.0. Center we found as deviation from the center of the vehicle.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `/advanced-lane-finding.ipynb` in the function `process_image`.  Here is an example of my result on a test image:

![alt text][image4]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a  [https://github.com/mvshalamov/CarND-Advanced-Lane-Lines/blob/master/output_video/project_video_output.mp4](./output_video/project_video_output.mp4)

#### Chalenge video
The main problem in this video^ that in some frame we don't find a lines and we must use result frome past frame.
Here's a [https://github.com/mvshalamov/CarND-Advanced-Lane-Lines/blob/master/output_video/project_video_output.mp4][./output_video/challenge_video.mp4]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

* problems arise in the moments when the shadow falls on the road marking
* problems arise in moments when a motorcycle or car drives in on one lane

For this cases we must found algorithm, which separates shadow well and other vehicles, mayby use ml.
