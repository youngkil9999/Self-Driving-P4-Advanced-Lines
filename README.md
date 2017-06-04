##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./output_images/1.png "Undistorted"
[image2]: ./output_images/2.png "Road Transformed"
[image3]: ./output_images/3_hls.png "Binary HLS_S Example"
[image4]: ./output_images/4_hls.png "Binary HLS_H Example"
[image5]: ./output_images/5_hsv.png "Binary HSV_V Example"
[image6]: ./output_images/6.png "Output"
[image7]: ./output_images/7.png "Output"
[image8]: ./output_images/8.png "Output"

[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/README.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

Used image from camera calibration folder.
with 8 points along x axis and 6 points along y axis

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
undistoted image



![alt text][image2]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

HLS, S threshold
![alt text][image3]
HLS, H threshold
![alt text][image4]
HSV, V threshold
![alt text][image5]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `PROJECT.ipynb` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```
src = np.int32(
    [
    [(img_size[1] / 2) - 75, img_size[0] / 2 + 100],
    [(img_size[1] / 2) + 105, img_size[0] / 2 + 100],
    [ img_size[1] / 2 + 600, img_size[0]],
    [ img_size[1] / 2 - 520, img_size[0]]])
dst_ = np.int32(
    [[(img_size[1]/ 2 - 60), 0],
    [(img_size[1] / 2 - 60), img_size[0]/2 + 80],
    [(img_size[0]), img_size[0]/2 + 80],
    [(img_size[0]), 0]])

```
This resulted in the following source and destination points:

| Source        | Destination   |
|:-------------:|:-------------:|
| 565, 460      | 580, 0        |
| 745, 460      | 580, 440      |
| 1240, 720     | 720, 440      |
| 120, 720      | 720, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image6]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image7]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `PROJECT.ipynb`

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `PROJECT.ipynb` in the function `process_image()`.  Here is an example of my result on a test image:

![alt text][image8]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](https://youtu.be/yP7EAUmVd_w)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

As I seen the video, it could be easily affected by sunlight or shades around tree. I could see combining more threshold could improve the quality but not sure could it be perfect not affected by any circumstances

Also, I could make a video file the way I could do but I actually wanted to use lane class as a Sanity check. but I need some example that could be referred.
I used videofileclip module for video editor. I am not sure there is a way to replace the current slope compared to old slope. you know with fl_image function I could only insert one parameter which is clip image. but If I want to compare previous left and right slope with current, I need to add more parameters in process_image function. I could do it by just saving each images in the folder and change them and made the video. step by step but I don't want to do it like that way. maybe I could figure out later if I keep studying on video python.
