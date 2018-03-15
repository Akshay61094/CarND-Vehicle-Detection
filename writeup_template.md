## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the Sixth code cell of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then tried different tried different values of the following parameters and visualize the result :

color_space

orient

pix_per_cell

cell_per_block

hog_channel

spatial_size

hist_bins 

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=12`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters , and finally trained them on the classifier. My final choice of the parameters was totally based on the result of the training accuracy of the classifier. After going through a lot of combination of parameters, I found that the below mention values of parameters gave me a good test set accuracy of 98.76%. The values of final parameters are :

color_space = 'YCrCb'

orient = 12

pix_per_cell = 16

cell_per_block = 2
 
hog_channel = 'ALL'

spatial_size = 32 X 32

hist_bins = 32

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM in the cell no 6  with comment 'Train Classifier'. The features extracted in the previous step were Standardized  by removing the mean and scaling to unit variance using a sklearn Scaler. Also the data was randomly shuffled and slpit into training and test data set for creating a classifier that generelises. After training i got a good training accuracy of 98.76% .

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I utilised the find_cars() function that was given in the classroom for implementing sliding window search, The code of function is available in cell 11 and it is implemented on the test images in cell 13. The Function performs a trick and finds out the hog features of all the region of interest at once and later on extrapolating individual hog features of cells.
The method performs a classifier prediction on the hog features for each window region and returns rectangle boxes for each window that gets a positive prediction.
The image below show the first detection of windows after implementing find_cars() function :

![alt text][image3]

I then used the following scale sizes on the mentioned region(Y values) (Basic idea was to restrict smaller window to farther region and larger window to closer region).

Scale 1 - from 400 to 480 px

Scale 1.5 - from 400 to 528 px

Scale 2 - from 400 to 564 px

Scale 3.5 - from 400 to 664 px

After applying the window sizes on the mentioned regions , I then combined all the windows found from these. As we know that we have to remove false positives from the images. For this we calculate the heatmap of the image. The add_heat() function basically adds one to all the pixel values of the detected windows. Generally , true positive is combined with multiple positive detections because of overlapping and multiple window sizes and false postive is accompanied by only 2 or 3 theirfore we can apply a threshold on the heatmap to remove false positive. The function apply_threshold() does the thresholding. 

Below is an image of heatmap without threshold:

After applying a threshold of 1 the image is shown below :

After heatmap is generated we use scipy.ndimage.measurements.label() find out spatially contigous areas and assign a label.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

The following images show the final output of pipeline on all the test images :

![alt text][image4]

To Optimize the performance of the classifier the parameter pix_per_cel was changed from 8 to 16 which reduced the feature vector size and also increased the execution speed along with accuracy, also color space was changed from HLS to YCrCb.


---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

For removing False positives and combining overlapping bounding boxes I took the combined heatmap of the last 10 frames and applied a threshold of 7 on the combined heatmap. After doing this the above problems were solved and a smooth bounding boxes were formed.




---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

