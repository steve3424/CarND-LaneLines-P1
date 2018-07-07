# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 11 main steps:
1. Convert the images to grayscale
2. Blur the images
3. Run Canny function to return edges
4. Conditional which checks the shape of the image and applies a region mask based on that. This is to accomodate the challenge video.
5. Run Hough function to return all of the lines in the specified region.
6. Pass the list of lines returned by Hough into draw_lines()

--the following steps include my modifications to the draw_lines() function

7. Pass the list of lines returned by Hough into get_mb() that then returns a 2D list of slope,intercept pairs for each line.
8. Pass the list of slope,intercept pairs into separate_lines that separates the slope,intercept pairs into two separate 2D lists. Positive slopes go into the right, negative slopes go into the left.
--I also included a threshold at this stage. This not only separated the slopes by positve or negative, but also filtered out slopes that were within a certain proximity of 0 as to not bring down the average at the next stage.
9. Pass the two filtered list of right and left slope,intercept pairs through avg_mb to get the average slope and average intercept for each right and left group of Hough lines.
10. Left with two slope intercept pairs, one average for the left and one for the right, I passed into extrapolate_line() these two lines as well as the Y coordinates for the top of my masked region (step 4) and for the bottom. These Y coordinates were added to the hough_lines() arguments and to the draw_lines() arguments. This function calculates the X coordinates based on the slope,intercept pair and the Y coordinates of the top and bottom of the region mask giving me the endpoints of the average lines along the entire region of interest.
11. Combine the image from draw_lines() and the original image.


### 2. Identify potential shortcomings with your current pipeline

If there is any section of the road where the lane lines are worn out or not clearly visible (not readily detected by Canny), my pipeline would likely fail. This is a case where the lane line is not detected at all. 

Also if there is too much noise, shadows in the case of the challenge video, my pipeline also would likely be affected. My primary method for filtering noise other than tweaking the parameters of the blur, Canny, Hough, etc. is to filter out Hough lines whose slope is too close to zero. This worked pretty well for this particular project, but it doesn't take into account errant intercept values. It won't filter out lines which have a slope similar to the lane lines, but are shifted far away from them.



### 3. Suggest possible improvements to your pipeline

A possible remedy to the first problem stated above would be a fall back line taken from previous frames. If the line is not detected at all and there is nothing in my region of interest mask to average or extrapolate, I could use a line that existed in a previous frame instead.

As for the second problem, there may be more optimal values for the Canny and/or Hough functions. Other than that having a more robust filtering of the detected Hough lines by incorporating intercept values could be very useful.

I could also introduce a running average which may help smooth out the lines. Some of them look like they've had too much caffeine. I will definitely be returning to this project in the future in order to pass the challenge video.
