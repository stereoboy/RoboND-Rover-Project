## Project: Search and Sample Return
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg 
[screenshot0]: ./screenshot0.png
[screenshot1]: ./screenshot1.png
[video]: ./test_mapping.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

The whole process is as follows
* wrap perspective images to bird-eye view images.
* filter the wrapped images using tuned RGB values.
```  
    # R > 160, G > 160, B > 160
    terrain = color_thresh(warped)

    # R > 90, G > 90 B < 60
    rock = np.zeros_like(terrain)
    rock[(warped[:,:,0] > 90) & (warped[:,:,1] > 90) & (warped[:,:,2] < 60)] = 1

    # anything else is obstacles
    obstacle = np.zeros_like(terrain)
    obstacle[(warped[:,:,0] > 0) | (warped[:,:,1] > 0) | (warped[:,:,2] > 0)] = 1
    obstacle[(rock==1) | (terrain==1)] = 0
```
* convert rover-centric coords
* convert world coords

Tuning thresholds for rock samples is a little hard for me.
The result is included in the [notebook](./code/Rover_Project_Test_Notebook.ipynb)

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 
Final Result is here:  [test_mapping.mp4](./output/test_mapping.mp4)

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

I implmented the perception_step() and decision_step().

![alt text][screenshot0]

Basic logic in `decision_step()` works well. But I changed some parameters.
```
-        self.stop_forward = 50 # Threshold to initiate stopping
-        self.go_forward = 500 # Threshold to go forward again
+        self.stop_forward = 600 # Threshold to initiate stopping
+        self.go_forward = 1200 # Threshold to go forward again
```
After several test, I have found the value of stop_forward (stop condition) is too low. Under the default setup the robot oftenly get stuck in obstacles. I have made stop condition more strict by increasing the stop_condition value.

#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

![alt text][screenshot1]

Unfortunately my development enviorment is not good, I need to set up the lowest resolution 640x480 when I run the simulator. The result image shows 78% map with 64% fidelity and 5 rock samples. All requirement are satisfied.
