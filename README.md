# Sensor Fusion Sensor Fusion
### 2D Feature Tracking Project

<img src="images/keypoints.png" width="820" height="248" />

## Overview

C++ development to build the feature tracking component of a camera based collision detection system. This implementation integrates several keypoint detectors and descriptors. Different combinations are evaluated for performance regarding number of key points and speed resulting in a proposal to be used in the final project: vehicle collision detection system.

---

## Installation steps

* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux| Mac)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)

---

## Build the code

1. Access the folder created from cloning `cd SFND_2DFeatureTracking`
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`

---

## Usage

1. From terminal window; change to build folder of project `cd SFND_2DFeatureTracking/build`
2. `./2D_feature_tracking`

---

## Model Documentation

### MP.1 Data Buffer Optimization

To prevent overloading the memory of the computer, a buffer which only holds a certain number of images in memory can be utilised. In order to only keep the images which are relevant the buffer size is fixed. When an image arrives the buffer size is checked. If the buffer is already at capacity then the oldest image is 'popped' of the end and the newest on is 'pushed' into the front of the buffer. The code for this implementation is shown at:

_Lines 85-95: MidTermProject_Camera_Student.cpp_


### MP.2 Keypoint Detection

The following detectors are implemented:
SHI-TOMASI, HARRIS, FAST, BRISK, ORB, AKAZE and SIFT

To select the detector type replace the string value of _vdetectorType_ with the desired detector type:
```
// set detector type
// "SHITOMASI"|"HARRIS"|"FAST"|"BRISK"|"ORB"|"AKAZE"|"SIFT"
string vdetectorType = "SIFT";
```
_Lines 22-24: MidTermProject_Camera_Student.cpp_

Implementation code for the selector can be viewed at:
_Lines 107-135: MidTermProject_Camera_Student.cpp_

The implementation of the algorithms for these selectors can be viewed at:
_Lines 162-286: matching2D_Student.cpp_

### MP.3 Keypoint Removal

In order to achieve a more targeted evaluation all of the feature points which are not on the target preceding vehicle are discarded. A bounding box with parameters cx = 535, cy = 180, w = 180, h = 150 is defined to represent the vehicle envelope. This is implemented in:
_Lines 142-158: MidTermProject_Camera_Student.cpp_


### MP.4 Keypoint Descriptors

The following descriptors are implemented:
BRISK, BRIEF, ORB, FREAK, AKAZE and SIFT

To select the descriptor type| replace the string value of _vdescriptorType_ with the desired descriptor type:
```
// set descriptor type
// "BRISK"|"BRIEF"| "ORB"| "FREAK"| "AKAZE"| "SIFT"
string vdescriptorType = "SIFT";
```
_Lines 26-28: MidTermProject_Camera_Student.cpp_

Implementation code for the selector can be viewed at:
_Lines 210-230: MidTermProject_Camera_Student.cpp_

The implementation of the algorithms for these selectors can be viewed at:
_Lines 74-118: matching2D_Student.cpp_


### MP.5 Descriptor Matching

FLANN matching and k-nearest neighbour (KNN) is implemented.

To select the different methods:
BRUTE FORCE matching or FLANN matching replace the string value of _vMatcherType_ with type:

```
// set the descriptor matching type
// MAT_BF| MAT_FLANN - only for SIFT
string vMatcherType = "MAT_FLANN";
```
_Lines 31-33: MidTermProject_Camera_Student.cpp_

To select the different methods:
NEAREST NEIGHBOUR or K-NEAREST NEIGHBOUR replace the string value of _vSelectorType_ with type:
```
// set the descriptor selector type
// "SEL_NN" "SEL_KNN"
string vSelectorType = "SEL_KNN";
```
_Lines 35-37: MidTermProject_Camera_Student.cpp_

Implementation code for the selector can be viewed at:
_Lines 241-252: MidTermProject_Camera_Student.cpp_

The implementation of the algorithms for these selectors can be viewed at:
_Lines 17-64: matching2D_Student.cpp_

To reduce the complexity of parameter setting and errors being generated the descriptorMethod which is passed into `matchDescriptors` is configured to automatically select the correct method dependent if the selected descriptor is 'binary' type or 'histogram of gradients' type.
_Lines 246-249: MidTermProject_Camera_Student.cpp_


### MP.6 Descriptor Distance Ratio

In order to reduce the quantity of false-positive matches in K-Nearest Neighbours algorithm a filter called Lowe's distance ratio is implemented. It compares distance two candidate keypoints and the one with the minimum distance is selected as a match. The threshold is set to 0.8 the implementation can be found at:
_Lines 56-64: matching2D_Student.cpp_

### MP.7 Performance Evaluation 1

The Number of keypoints within the vehicle envelope(ROI Points) for each image is shown in the csv file: `SFND_2D_FEATURE_TRACKING_RESULTS.csv TAB: MP7_PERFORMANCE 1`

The keypoint size is the size of the region that is used to form the keypoint description, the higher this value the higher probability the feature will be recognised across multiple instances of an object. The figure shown is the sum of all the regions for all the keypoints.
This sum is then shared between the keypoint count to establish an average neighbourhood size.

Using the neighbourhood size the table is sorted for probability that keypoint features would be recognised across multiple instances. FAST was prioritised over AKAZE as although it scored the same, it has a larger quantity of keypoint features.

Top 3 for Feature detections: ORB, BRISK, FAST

| Detector | KEYPOINTS | SIZE ACCUMULATION | NEIGHBOURHOOD SIZE |
| :---:    | :---:  | :---:  | :---:  | 
| ORB | 116 | 6500 | **56** | 
| BRISK | 276 | 6061 | **21** |
| FAST | 409 | 2865 | **7** |
| AKAZE | 167 | 1285 | 7 |
| HARRIS | 24 | 148 | 6 |
| SIFT | 138 | 698 | 5 |
| SHI-TOMASI | 117 | 471 | 4 | 


---

### MP.8 Performance Evaluation 2

Using all possible combinations of detectors and descriptors the number of matched keypoints for all 10 images is recorded.
The matcher type is set to BRUTE FORCE, which will try to find the closest descriptor in whole set by trying each one.
The K-Nearest Neighbour selector is utilised with a distance ratio set to 0.8.

The average number of matched points for each combination is shown:
To view the full data refer to csv: `SFND_2D_FEATURE_TRACKING_RESULTS.csv TAB: MP8_PERFORMANCE 2`

The 3 highest matched keypoint count after filtering out false positives in order of highest to lowest is:

Detector/Descriptor

1. FAST/BRIEF
2. FAST/SIFT
3. FAST/ORB

| Descriptor   <br /> <br />  Detector  | BRISK | BRIEF | ORB | FREAK | AKAZE | SIFT |
| --: | :-: | :-: | :-: | :-: | :-: | :-: |
| SHITOMASI| 85 |104|100|85|N/A| 103|
| HARRIS | 15|19 |17|16| N/A| 18|
| FAST| 242 | **314** | **306** |247|N/A|**309**|
| BRISK| 174 |189|167|169|N/A|192|
| ORB| 83 |60|84|46|N/A|84|
| AKAZE| 135|140|131|132|139|141|
| SIFT| 65 |78|N/A|66|N/A|88|

Combinations with N/A are not valid detector/descriptor combinations. Error handlers were placed in the code in the event these combinations are tried. The program simply exits and displays an error message informing this.
_Lines 218-223: MidTermProject_Camera_Student.cpp_


### MP.9 Performance evaluation 3

The table below shows the times for total detection + descriptor + matching process with the parameters set to brute force and k-Nearest Neighbour with 0.8 ratio. The complete table is shown in the csv file: `SFND_2D_FEATURE_TRACKING_RESULTS.csv TAB: MP9_PERFORMANCE 3`

I evaluated the 3 stages total average processing duration to understand the best performance. I also assessed based on median total too, as with some combinations an initial overhead could be noticed which could have skewed the average results; however this did not affect the final outcome. FAST detector generated the fastest times, even though its matching time was the highest of all algorithms.


The 3 fastest times for generating matched keypoint count after filtering out false positives in order of fastest to slowest is:

Detector/Descriptor

1. FAST/BRIEF
2. FAST/ORB
3. FAST/BRISK

| Descriptor   <br /> <br />  Detector  | BRISK | BRIEF | ORB | FREAK | AKAZE | SIFT |
| --: | :-: | :-: | :-: | :-: | :-: | :-: |
| SHITOMASI| 9.63 | 9.21 | 10.73 | 28.32 | N/A | 21.87 |
| HARRIS | 11.5 | 10.94 | 13.03 | 29.39 | N/A | 19.35 |
| FAST| **6.22** | **3.21** | **5.41** |23.34| N/A | 16.29|
| BRISK| 52.51 | 49.73 | 57.15 | 73.71 | N/A | 92.24 |
| ORB| 7.22 | 7.0 | 15.06 | 24.81 | N/A | 29.33 |
| AKAZE| 62.61 | 59.52 | 65.37 | 84.47 | 105.19 | 77.26 |
| SIFT| 82.71 | 79.74 | N/A | 100.84 | N/A | 130.08 |


---

## Reflection

Utilising a simple weight scoring system of 5 for best, 1 for worst; the top 3 of each performance category is evaluated. The results are summed and can be viewed in the table below.

| Descriptor | Detector | Time | Weight| KeyPoint Count | Weight| Quality | Weight | Score |
| --: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| FAST| BRIEF  | 3.21 | 5 | 314 | 5 | 7 |1 |11 |
| FAST| ORB  | 5.41 | 3 | 306 | 1 | 56 | 5 |9 |
| FAST| SIFT  | --- |--- | 309 | 3 | 7 |1 |4 |
| BRISK| ---  | --- | --- |--- |--- | 21 |3 | 3 |
| FAST| BRISK  | 6.22 | 1 | --- | --- | 7 |1 |2|

The final top 3 results are:

Detector/Descriptor

1. FAST/BRIEF
2. FAST/ORB
3. FAST/SIFT

The recommended best choice for the purpose of detecting keypoints on vehicles based on this study would be the FAST/BRIEF detector/descriptor combination. 
However the FAST/ORB comes very close and as the number of keypoint matches is very similar, the trade off would be quality versus speed.
The BRIEF descriptor is just under twice as fast, but the quality is 8 times less than when used with the ORB.
The time penalty is 2.2ms, but having a much higher chance of matching the same feature in other frames might be acceptable once integrated within the whole system.

My recommendation for the purpose of detecting keypoints on a vehicle with intention to utilise for collision avoidance would be the higher quality/ higher confidence keypoint detections, as the time difference in processing will be dwarfed by the time it takes to acquire the image. The small time difference could be removed completely by avoiding dealing with downstream processing of increased false positives.

RECOMMENDATION: **FAST/ORB** DETECTOR/DESCRIPTOR for collision avoidance system.


---

## License

For License information please see the [LICENSE](./LICENSE) file for details

---



