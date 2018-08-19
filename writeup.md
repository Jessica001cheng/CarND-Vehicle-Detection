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
[image1]: ./output_images/example_car_noncar.png
[image2]: ./output_images/Car_ORT9_PIX16_CEL2.png
[image3]: ./output_images/NonCar_ORT9_PIX16_CEL2.png
[image4]: ./output_images/scale1_slide_window.png
[image5]: ./output_images/scale1_5_slide_window.png
[image6]: ./output_images/scale2_slide_window.png
[image7]: ./output_images/scale3_slide_window.png
[image8]: ./output_images/drawbox.png
[image9]: ./output_images/test_heat.png
[image10]: ./output_images/test_heat_thres.png
[image11]: ./output_images/test_label.png
[image12]: ./output_images/test_drawlabel.png
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the 5th code cell of the IPython notebook with function name: get_hog_features.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

I use ipython interactive widget to show the HOG images when in different parameters. The code is in 7th & 8th code cell of the IPython.

Here is an example using the `RGB` color space B channel and HOG parameters of `orientations=9`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:

car image:

![alt text][image2]
non car image:

![alt text][image3]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and check the feature vector lenght with different parameters.
The feature extract time on different parameters is recorded in "testResult.csv" file

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using different HOG features which are got from different HOG parameters. The classifer accuracy is recorded in "testResult.csv" file.

With comparing the extract time and classifier accuracy, I decide to use below parameters:
`colorspace = 'YUV'`
`orient = 11`
`pix_per_cell = 16`
`cell_per_block = 2`
`hog_channel = 'ALL'`



### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to search with below procedure:

1. start search from y pixel: 400, because we dont need to search car in sky. The scale is 1.0, means use 64x64 window. 
2. also search from y: 416 with scale 1.0 to search with overlap windows.
![alt text][image4]
3. search from y: 400 with scale 1.5, window 96*96. 
4. Also earch from y: 432 with scale 1.5, window 96*96.
![alt text][image5]
5. search from y: 400 with scale 2.0, window 128*128. 
6. search from y: 432 with scale 2.0, window 128*128.
![alt text][image6]
7. search from y: 400 with scale 3.0, window 196*196.
8. search from y: 464 with scale 3.0, window 96*96.
![alt text][image7]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I searched on two scales using YUV 3-channel HOG features in the feature vector, which provided a nice result but with some false postision.  Here are some example images:

![alt text][image8]

The false position is removed by thresholded heatmap and lables in below session.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_out.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

1. I use heatmap and thresholded the heatmap to identify the vehicle position. Below is the heatmap of test images.
![alt text][image9]
thresholded heatmap of test images. The false position are removed well.
![alt text][image10]


2. then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected. Below is the label and bounding boxes of test images.
![alt text][image11]

bounding boxes:
![alt text][image12]

3. then in pipeline, i record last 16 frame heat maps. Generate labels based on last 16 frames. It smoothes the bounding box position well.





---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I think this pipeline is very slow to handle the real time cases as the building tims is around 5min 11s. It is not efficient to search the whole half of the image. Maybe we can try to search the right/left downside of the image to improve.


