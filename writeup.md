
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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[original]: ./output_images/original.jpg "Original"
[binary_warped]: ./output_images/binary_warped.jpg "binary_warped"
[color_binary]: ./output_images/color_binary.jpg "color_binary"
[combined_binary]: ./output_images/combined_binary.jpg "combined_binary"

[sobel_x]: ./output_images/sobel_x.jpg "sobel_x"
[sliding_window]: ./output_images/sliding_window.jpg "sliding_window"

[lane_img_wraped]: ./output_images/lane_img_wraped.jpg "lane_img_wraped"
[lane_only]: ./output_images/lane_only.jpg "lane_only"
[line_in_image]: ./output_images/line_in_image.jpg "line_in_image"

[undistort]: ./output_images/undistort.jpg "undistort"

[video1]: ./project_video.mp4 "Video"

---


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for camera calibration is in the `lane_line.ipynb`. I read all the images in 'camera_cal' directory and find chessboard with 9*6 matrix. With 20 images, 17 images we are able to find the chessboard corner.
With these images, I found all the object points and images points and find the camera matrix with `cv2.calibrateCamera`. I store it output `mtx` and `dist` in to pickle file.


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.


With the camera matrix, I can undistort the image with `cv2.undistort`.
 * Original image
![original][original]
 * Undistorted image
![undistort][undistort]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I use both color thesholds and x gradient for line image detection. I tried with y sobel and mag sobel, the x sobel generates best results from gradients point of view.

* Sobel x
![Sobel x][sobel_x]

* Color threshold
![color][color_binary]
* combine color + sobel
![combined_binary][combined_binary]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The perspective transform is the one that take some time to understand. 'cv2' provided good utility class to perform 'getPerspectiveTransform', but actually found the src and dst requires a lot of iteration. I am using the starting points from Q/A for this project. The src points will be calculated based on image side and it will take `bot_width`, `mid_width`, `height_pct`, `bot_pct`, and use a small offset for transform.

* binary line after perspective transform

![binary_warped][binary_warped]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I use the sliding window approach, and use the 9 windows to fit the image post perspective transform.

![sliding_window][sliding_window]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I am using approach described in class for calculate the radius for the curvature. Basically, use the left lane points and right lane points to fit in polynomial form and use the radius from the polynomial form.
The calculate the offset, I am using the middle points of the left lane and right lane bottom, and compare with the center of the image.
Both methods utilize the information about standard us lane size 3.7 meter wide.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

* Lane area in wrapped image
![lane_img_wraped][lane_img_wraped]
* Lane area post perspective transform
![lane_only][lane_only]

* lane in original image
![line_in_image][line_in_image]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I think the perspective transforms is the one that takes a lot of time to understand and process, since I start with the thinking that I need different parameter for each image, and need to change it dynamically. The existing parameter for transforms will need to turned for different video or different car.  

To make the pipeline more robust, I think we can implement look ahead filter which each frame of video can utilize the information from previous detection and the output will not change significantly for each frame.
