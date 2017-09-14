# **Finding Lane Lines on the Road** 

The code in this project determines lane lines for images and video from a camera mounted on a self driving car.  
<br />
<br />
<figure>
 <img src="test_images/solidWhiteRight.jpg" width="380" alt="Original Image" />
 <figcaption>
 <p></p> 
 <p style="text-align: center;"> An original input image </p> 
 </figcaption>
</figure>
<br />
<br />

The lane lines are calculated and then annotated directly on the image/video using colored lines.
<br />
<br />

<figure>
 <img src="examples/laneLines_thirdPass.jpg" width="380" alt="Annotated Image" />
 <figcaption>
 <p></p> 
 <p style="text-align: center;"> Image annotated with lane lines </p> 
 </figcaption>
</figure>

<br />
<br />

To do this, a _pipeline_ of python code using opencv, numpy, and scipy libraries was developed to perform the lane line calculations and image annotation.


## Reflection

### 1. The Pipeline

The pipeline for generating left and right lane lines on an image of the road does the following:

1. converts input image to gray scale
2. applies Gaussian blur to gray image
3. uses Canny edge detection to determine image feature boundaries
4. determines an area of interest, a trapezoid, in lower part of image where lane lines are most likely to be found and applies this as a mask to the image
5. uses Hough transform to determine the line segments within area of interest
6. calculates the left and right lane lines from the line segments and draws them in given color and thickness
7. finally, applies the colored lane lines to the original image with some level of transparency 

In particular, step 6 above (calculating lane lines from a set of line segments) involved several sub steps.

1. The line segments had to be determined to be either part of the left lane line or right lane line.  This was done using slope (positive slope was left lane, negative slope was right lane) 
2. the line segments were then deconstructed into individual points and collected into an array of points for each line
3. a linear regression was then applied to determine the best fit line for these points (for each left and right)
4. these two best fit lines were then extrapolated to cover the entire area of interest in the image
5. the extended lane lines were then rendered on the image with color and thickness


### 2. Shortcomings with current pipeline


There are two main shortcomings.  First, several parameters such as Hough transform parameters, area of interest dimensions, Gaussian blur parameters, etc. are hard coded within the functions instead of passed as function parameters into process_image() function.  In particular, the draw_lines() function has to know dimensions of area of interest in order to properly extrapolate the lane lines; however, it does not have this information so it had to be hard coded - a second time!

The second main shortcoming is the assumption that the lanes will always fall within the trapezoidal area of interest.  Since the camera is mounted on the car, as he car swerves, pitches up or down, skids, etc., the lanes might drift outside of the pre-defined area of interest and so we will lose lane definition.


### 3. Possible improvements for the pipeline

The two improvements I would suggest both address the hard coding of parameters.  The first solution would be to build a config object of all relevant parameters that is passed in to process_image() which can then in turn pass the configuration to any of the helper functions that require these parameters as well.

Another solution would be to design a class whose instance variables are the various parameters and whose methods are all of the helper functions, and whose main public method is process_image() method above.  We could simply instantiate the class, populate it with desired parameter values, and then call the process_image() method.
