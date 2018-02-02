# Vehicle Detection Project

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1a]: ./results/cars.PNG
[image1b]: ./results/not_cars.PNG
[image3]: ./results/sliding_windows.PNG
[image4a]: ./results/res_1.PNG
[image4b]: ./results/res_2.PNG
[image4c]: ./results/res_3.PNG
[image4d]: ./results/res_4.PNG
[image4e]: ./results/res_5.PNG
[image4aa]: ./results/res_1a.PNG
[image4bb]: ./results/res_2b.PNG
[image4cc]: ./results/res_3c.PNG
[image4dd]: ./results/res_4d.PNG
[image4ee]: ./results/res_5e.PNG
[video1]: ./final_project_video.mp4

---

### Training data and feature extraction

The code for this step is contained in the first code cell of the IPython notebook (or in lines # through # of the file called `some_file.py`).  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

### Vehicles
![alt text][image1a] 

### Non-vehicles
![alt text][image1b]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

The YCrCb colour space was chosen to be the most effective in detection. Conversion from RGB to YCrCb reduces the colour information encoded in the image but amplifies the intensity of the pixels and through trial and error, this was found to be the most effective method to apply the histogram of gradients. 

The following parameters were used along with the `YCrCb` color space: HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

I tried various combinations of parameters and found that the chosen HOG paramters yielded the best combination of speed and performance.

Once the training data was collected, a feature vector consisting of HOG features, histogram of color features and spatial binning of pixels was collected for each image using a function called extract_features. Once these features were collected, the data was broken down into testing and validation data using a 80-20 split and run through a Support Vector Classifier (SVC). The classifier was trained on 7872 features for 35520 samples of data. The validation set accuracy of the data was 0.992.

### Sliding Window Search

During the implementation of the algorithm, a sliding window search was done on each of the images to extract the various features to run through the classifier. The size of the cells was played around with to figure out what number of cells yield the best combination of accuracy and speed. Significant overlap was used in the sliding window process to ensure that key features were not missed.

![alt text][image3]

### Single Frame Implementation

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a decent result.  Below are some example images (Images on the left are the processed images and amges on the right are heatmaps after a threshold has been applied) :

![alt text][image4a]  ![alt text][image4aa]
![alt text][image4b]  ![alt text][image4bb]
![alt text][image4c]  ![alt text][image4cc]
![alt text][image4d]  ![alt text][image4dd]
![alt text][image4e]  ![alt text][image4ee]

---

### Video Implementation

Here's a [link to my video result](./final_project_video.mp4)

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:


---

### Discussion/Reflection

The techniques used in this project are acceptable for a beginner project ot introduce them to basic concepts. The algorithm as it stands currently, does not work as well as hoped for edge cases and is very naive in many regards. If I were to extend this project and spend some more time on it, I would figure out a way to use instance segmentation to identify the vehicles. I believe that method is more robust and despite its high computational cost, would yield incredibly accurate results and be able to identify most of the objects in the frames. 
