# **Finding Lane Lines on the Road** 

## Finding Lanes

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The goal of my pipeline is to detect lane pines. To achieve this, I am using [1] canny edge detection to detect edges, [2] Hough transformation to build lines of connected points, and finally some [3] additional custom logic to select only lines of interest and [5] draw final guide lines. Here is this process in more detail:

1. __Convert to grayscale__ -  not much to say here, needed for canny algorithm
![alt text][gray image]
2. __Noise reduction__ - Canny can detect too many edges creating too much noise. I am using gaussian blur to smooth averages and obtain only more major edges. I have not found much improvement with playing around with the kernel size and I kept it at 7.
![alt text][blured image]
3. __Edge detection__ - Step where I detect edges using Canny. The only parameteres I could have tweek were thresholds and after some experimentation I chose 50 and 150 (which also follows lecture's recommendation)
![alt text][canny image]
4. __Area of interest__ - I use bitwise AND mask to select a region of an image that roughly covers areas there a road could be expected.
![alt text][aoi image]
5. __Draw edges__ - I use Hough transformation to draw all the detected edges. Parameters chosen empirically.
![alt text][hough image]
6. __Draw lanes__ - Final bit of logic that takes in all the detected edge lines and produces a final lane:
  1. Filter out all lines who's slope does not fit within a certain range of slopes expected for lanes.
  2. Collect all remaining start/end points into lists for left and right lanes (based on slope)
![alt text][final almost image]
  3. For each frame, and for both left and right lanes, take the trailing X points and fit a line through them. This gives m and b (from y = mx + b)
  4. Draw a lane as follows: assume y1 is bottom of the image and y2 is top of the area of interest. Using m and b obtained in the previous step, find x1 and x2. Given (x1,y1), (x2,y2), draw a final lane.
![alt text][final image]




### 2. Identify potential shortcomings with your current pipeline

This pipeline was build specifically for the videos provided with this assignment. For this reason, parameters are tuned for these specific videos, which means:

1. Picks up edges that logically should not be part of the lane.
2. Not adaptable for different time of day (shade, sun, etc...)
3. Not adaptable for curvy roads
4. Does not detect multiple lanes (considers whats just in front of the car, not to the sides)
5. Not resilient to noise

### 3. Suggest possible improvements to your pipeline

1. Insead of fitting lanes to a linear function, use polynomial, which would fit curves better.
2. Use logic to determine probability of an edge belonging to line. If an edge is detected close to where previous lane was, then it is probably it is part of the lane. If edge is detected in between lanes, then this probability is lower.


[//]: # (Image References)
[gray image]: ./images/lines-gray.jpg "Grayscale"
[blured image]: ./images/lines-gray_blured.jpg "Blured"
[canny image]: ./images/lines-cannied.jpg "Cannied"
[aoi image]: ./images/lines-aoi.jpg "Area of Interest"
[hough image]: ./images/lines-hough.jpg "Hough"
[final almost image]: ./images/lines-final_test.jpg "final almost"
[final image]: ./images/lines-final.jpg "final"