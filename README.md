
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



Camera Calibration:

1.Camera Calibration is the first,foremost and most important part of our projct.As we are doing lane detection which involves nothing but taking images frame by frame and detecting lane lines.So any error in pic due to camera lense will distort the images and our calculation . So to start with camera calibration we take images with camera of chess board and mark the corners on x and Y dimensions.So for the prj we had 9*6 corners. 
These corners are found out using CV2function findcornere and are stored in IMgpoint, the relative object points in 3 dimension are formed and used in CV2.calibratecamera.This function resturns calibration matrix which is used further to undistort other images.
Results of camera calibration are as follows.

![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/result_cal/test_undist1.jpg)
![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/result_cal/test_undist5.jpg)





2.Display undistorted images
The function camera Calibration return the calibration matrix, which is used in getting undostorted images.The result of undistoreted images on our test images is as follows:
![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/test_undist10.jpg)



3.Thresholding Images:
Now once we have calibrated Images we will see test result on Test images.
Before that we will do some preprocessing using a pipeline of function call to detect lines and edges.
Here the parameters of images are adjusted using any combination of Sobel x,Sobel y,MAgnitude (x,y),Dir(x,y),and in HLS. We use S(Saturation) transform which gives very good results for lane and edges.While sobel is a matrix operator which has derivate of x, y.
We can use any combination of all these different parameter to get desired thresholded images. I used widget tuner in a different notebook and passed the final parameters to the function Update transform.
It helped me get Optimal tuning of params.I discared Direction threshold as It was passing exponenatial small nos which was giving 0 mean to our histogram detection.Please look at update_params function in notebook to check the thresholding technique.
The result of thresholding on  test images is as follows:
![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/untitled.png)
![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/untitled2.png)
![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/untitled3.png)


3)Then the output binary thresholded image is applied a mask to get only thresholded images.This is then passed to cal_unwarp function to get the birds eye view.where the lanes from camera are project as top view images by transformation.To do this transformation we need 4 points on src which form a rectangle be mapped to 4 points on destination to form a rectangle. We use cv2 getperspective function which returns matrix for Warping which when passed to cv2 warpPerspective function with any undistorted image gives us the top view of image.
src = np.float32([[585, 460], [203, 720], [1127, 720], [695, 460]])
dst = np.float32([[320, 0], [320, 720], [960,720], [960, 0]])
Then these lane lines which are paralled will be used in our calculation
I used src dst points given in readme.md and used test images with staright lines to detect the line so that parallel can be viewed and thus verified.
The rsult of bird tranform images are as follows:

![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/image_birds eyeview.png)

4)
We use polyfit which fits the line and returned leftfit and rightfit are coeffeicents which are used to find points on line, curvature and center offest.
Window search method is used once and then once a line is located next iamge frame lines are serched in neighboring area of previous frame.
find_lanes_window()and search_lines_ROI() are helper function for same.
The result by using search_window method is as follows:
![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/histogram.png)

5)Then we need to calculate radius of curvature to see that we are doing well in lane detection and there are no abusrd measuremnts happenig. Also the caluction of offset of car from center lets us know where is
car placed on lane.
radiusOfCurvature(),sanitcheck() are helper function for this section.
Sanity check checks if we have lane detected.I wanted to check the slopes of line are smae to say they are parallel but I dint get same value to say tey are parallel.
To calculate radius we used polyfit functions coefficents Rcurve=(1+(2Ay+B)**2)**1.5/2A

6.Finally we process images using Pipline function and get output image marked with road marking,threshold images, birds eye view image, and text about center offset rt curve left curve.
Look at the image below:

![alt tag](https://github.com/MumMumMum/test_upload/blob/master/output_images/pipeline.png)
---

Pipeline (video)

Link to video:

Here's a [link to my video result](https://github.com/MumMumMum/test_upload/blob/master/output_video/output_lane_detect1.mp4)

---

Discussion

1. In implementaion the issue I faced was calculating the radius.Sanity check also it may fail its not robust.I wanted to check slope using y1-y2/x1- x2 to say its parallel line which i dint get accurate answers.
Also the thing mentioned of lane class which if used could help me reset my refrence line data .
Smoothing over frames also is not done .
But i was very happy with widget tuner with which my thresholding was good and there was no hitch in detecting lanes.
https://github.com/MumMumMum/test_upload.git heres a uploaded repo.
the video detection just passes the  "project_video.mp4" challenge.
By tuning thresholding we can delete the shadows in "challenge_video.mp4" to improve results.
Also by adding class lane improvement in smoothing can be achieved and reseting be done more technically correctly.

