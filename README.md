3D road line extraction, bird's-eye view projection, and junction detection for ROS stereo images
===============

Finds the middle line of a road, and its junctions, in a stereo image. Tested with ROS+Gazebo (DRC Atlas robot), but should work with any stereo camera as input. 

A Convolutional Neural Network, [CN24](https://github.com/cvjena/cn24), is used for road detection. Subsequently, a birds-eye view is calculated using OpenCV's findHomography and warpPerspective. Line segments are obtained by thinning the detected road, simplifying with a voxel grid filter, and calculating a minimum spanning tree of the resulting points.

Usage
===============

Requires:
- [CN24](https://github.com/cvjena/cn24) - the environment variable 'CN24PATH' has to be set to the path to the cn24 build directory (e.g. 'export CN24PATH=/home/USER/cn24/build')
- numpy, scipy, matplotlib 
- skimage
- cv2
- pcl (optional)
- ROS and rospy to capture image data (optional)

Usage example:

```python
import matplotlib
import matplotlib.pyplot as plt
from roadlinesegments import *

## uncomment to obtain images from ROS topic
#from savesensordata_node import *
#import time
#savesensordata()
#time.sleep(2)

## load images
camimg = getcameraimg()
roadimg = getroadimg()
depthmap, points, notnanindices = getdepthimg()

## get birds-eye view
warpedimg, H = getbirdseyeview(camimg, roadimg, depthmap, points, notnanindices)

## run road detection   
points, edge_list, junctionpointidx, allpoints = getroadlinesegments(camimg, warpedimg, H)

## plot results
plt.subplot(1,2,1)
camimg[roadimg!=0] += [0,100,0] # paint road green for visualization
plt.imshow(camimg)
plt.title("Original image and road detection")

plt.subplot(1,2,2)
plt.imshow(warpedimg)
for edge in edge_list:
    plt.plot([points[edge[0], 0], points[edge[1], 0]], [points[edge[0], 1], points[edge[1], 1]], c='r')
for i in junctionpointidx:
    plt.scatter(points[i, 0], points[i, 1], c='red', s=300)
plt.title('Birds-eye warped image, road skeleton and junctions')
plt.show()
```

Examples
===============

Detected road in first-person camera image (left), obtained by a simulated robot, and the same image warped to bird's eye perspective (right)
![Example 1](example_results/results_environment_1.png)

Actual environment
![Example 1](example_results/actual_environment_1.png)

Detected road in first-person camera image (left), obtained by a simulated robot, and the same image warped to bird's eye perspective (right)
![Example 1](example_results/results_environment_2.png)

Actual environment
![Example 1](example_results/actual_environment_2.png)

