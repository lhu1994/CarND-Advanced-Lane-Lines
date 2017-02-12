# Advanced Lane Finding Report

In this project, I build a pipeline which can detect current lane of a car and compute the lane's area, curvature. The pipeline is robust enough such that it can perform well in various color and lighting conditions of roads and lane lines

My pipeline is composed by the following steps  
    __1. Camera Calibration and Original Camera Image Undistortion__  
    __2. Mixed Technique Binary Thresholding of Undistorted Image__  
    __3. Bird View Perjection from Forward Facing Image__  
    __4. Line Search and Second Order Curve Fitting__  
    __5. Curvature Computation__  
    __6. Project Results Back to Original Camera Image__  
    
    
## Camera Calibration and Original Camera Image Undistortion
Why camera calibration?

Modern camera uses lenses and lenses introduce distortion to images.

For example:
![A Chess Board Image](https://github.com/CreatCodeBuild/CarND-Advanced-Lane-Lines/blob/master/camera_cal/calibration5.jpg)
As you can see, the chess board is curved. This is a distortion because in real life, this chess board has straight lines.

__Camera calibration__ is the process which finds how much a given camera distorts its images from real life objects. __Image Undistortion__ is the step which uses a given camera calibration information to undistort images taken by this camera.

__Each camera has its own calibration__. You can't use camera A's calibration to undistort images taken by camera B, unless camera A and B have the same calibration.

### Method
Chess board iamges are extremely useful for camera calibration, because we know the exact shape of a given chessboard and a chessboard has straing lines, which make the computation rather easy.

Our goal is to calibrate the camera which shot the driving video. Therefore, we gathered several chessboard images taken by the same camera.

             1             |            2              |             3
:-------------------------:|:-------------------------:|:-------------------------:
![](camera_cal/calibration1.jpg)  |  ![](camera_cal/calibration2.jpg)  |  ![](camera_cal/calibration3.jpg) 
![](camera_cal/calibration4.jpg)  |  ![](camera_cal/calibration5.jpg)  |  ![](camera_cal/calibration6.jpg)
![](camera_cal/calibration7.jpg)  |  ![](camera_cal/calibration8.jpg)  |  ![](camera_cal/calibration9.jpg)

I used 20 images in total. You can find all of them in [this folder](https://github.com/CreatCodeBuild/CarND-Advanced-Lane-Lines/tree/master/camera_cal)

In __helper.py__, you can find `class Calibrator`. I use this class to do both calibration and undistortion.  

The 3 key functions are `cv2.findChessboardCorners`, `cv2.calibrateCamera` and `cv2.undistort`.

You can find the math equation at 

You can run `test_Calibrator` function in __test.py__ to test this class. `test_Calibrator` will write undistorted images to directory [output_images/calibration](https://github.com/CreatCodeBuild/CarND-Advanced-Lane-Lines/tree/master/output_images/calibration)

## Mixed Technique Binary Thresholding of Undistorted Image
After first step, we need to binary thresholding this image so that we remove unnecessary information from this image.


  Undistored Color Image   | Binary Thresholded Image              
:-------------------------:|:-------------------------:
![](test_images/test2.jpg)  |  ![](output_images/threshold/combined_threshold_test2.jpg)
![](test_images/test5.jpg)  |  ![](output_images/threshold/combined_threshold_test5.jpg)

As you can see, the thresholding is robust regardless the color, shadow and lighting of the line, road and environment.

### Method
I combined x-direction gradient threshold and S channel threshold of the HSL color space.

  Color   |  X Gradient | S Channel | Combined              
:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:
![](test_images/test2.jpg)  | ![](output_images/threshold/gradient_threshold_test2.jpg) | ![](output_images/threshold/s_threshold_test2.jpg) | ![](output_images/threshold/combined_threshold_test2.jpg)
![](test_images/test5.jpg)  | ![](output_images/threshold/gradient_threshold_test5.jpg) | ![](output_images/threshold/s_threshold_test5.jpg) | ![](output_images/threshold/combined_threshold_test5.jpg)

The reason to apply a combined threshold is that any single threshold technique is not robust enough to detect lines in various condition.

#### Gradient Threshold
I applied sobel operator to compute the gradient. There are x direction and y direction gradient.

Original | Sobel 
:-:|:-------------------------:
![](https://d17h27t6h515a5.cloudfront.net/topher/2016/December/584cc3f4_curved-lane/curved-lane.jpg)| ![](https://d17h27t6h515a5.cloudfront.net/topher/2016/December/5840c575_screen-shot-2016-12-01-at-4.50.36-pm/screen-shot-2016-12-01-at-4.50.36-pm.png)

