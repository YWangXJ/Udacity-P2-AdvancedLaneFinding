
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

[image1]: ./output_images/undist.jpg "Undistorted"
[image2]: ./output_images/Road_undist.jpg "Road Transformed"
[image3]: ./output_images/BinaryImg.jpg "Binary Example"
[image4]: ./output_images/PerspectiveTransform.jpg "Warp Example"
[image5]: ./output_images/lane_line_pixels.jpg "Fit Visual"
[image6]: ./output_images/final_img.jpg "Output"
[video1]: ./project_video_output.mp4 "Video"


### The code for this project is contained in the IPython notebook located in "./Pipeline.ipynb" 
  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.


I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

In the function `undist`, I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Color transforms, gradients or other methods to create a thresholded binary image. 

I used a combination of color and gradient thresholds to generate a binary image in function`binary_img()`.  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `Perspective_Transform()`.  The `Perspective_Transform()` function takes image (`img`) as inpy.   source (`src`) and destination (`dst`) points are defined inside this function. I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([(x_offset,img_size[0]), (x_center-54, 450), (x_center+54, 450), (img_size[1]-x_offset,img_size[0])])
dst = np.float32([(x_offset,img_size[1]), (x_offset,0), (img_size[0]-x_offset, 0), (img_size[0]-x_offset,img_size[1])])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Identify lane-line pixels and fit their positions with a polynomial?

Then I create a funtion `find_lane_pixels()` and `fit_polynomial()` to fit my lane lines with a 2nd order polynomial. This function takes the warped binary image from previous step. The output is like this:

![alt text][image5]

#### calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

`curvature_vehpos_real()` function takes the img output from the ploynomial fit function and return the curvature of polynomial functions in meters, vehicle offset to lane center, and top/bottom lane width of the ROI.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines through the function `draw_result()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Additional Functions
`search_around_poly()` is used when there are previous line information generated from previous steps;
`sanity_check()` is to check if both lines have similar curvature, roughly parallel and are separated by approximately the right distance horizontally
`update_lines()` is to update the class Lane, i.e., update the detected lane information
`video_processing()` is the main function that takes each video frame and processes the video 

#### 2. Overall Pipeline
1) calibrate camera
2) undistort the camera input
3) calculate the binary output based on two threshold criteria
4) perspective transform
5) detect lane under different situations
6) calculate radius of curve and position of vehicle
7) draw final img        
#### 2. Final video output.  Your pipeline should perform reasonably well on the entire project video 

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

To process the video input, additional functions are needed to improve the performance and accuracy. `sanity_check()`  is very importance to let shown lines make sense. However, there are still some small errors at the far end of the detected line when the vehicle is at a curve or the color of road surface is changing significantly. But none of them could cause car collision or system failure. Further fine tunning on thresholds in fuction `binary_img()` and `santiy_check()` is needed. Also,the processing efficiency needs to be signifficantly improved. It takes me around 25 min to process a 50-second video, which won't be applicable for a real-time detection.

