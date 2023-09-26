# ITÜNOM
The UAV was designed to attain completion in all missions. It is able to fly and land autonomously, follow waypoints with high accuracy, carry and drop a UGV, generate a map from captured images, search for standard and emergent objects and avoid both stationary and moving obstacles.
## Initial Decisions
The first decision was selecting a vehicle type. A fixed wing vehicle type was chosen, and other structural aspects were focused on to provide sufficient stability for the autonomous detection and mapping missions and maneuverability for precise and time efficient flight.	
## Imaging Systems
The choice of camera was based on certain requirements. The camera is required to support high resolution to be able to capture images of small ground objects from a high flight position. Flight speed is also taken into consideration which is why a sufficiently high shutter speed is necessary to prevent capturing blurry images during high-speed flight. The camera must also support an application program interface (API). Sony 𝛼6000 camera has been chosen for the mission. A gimbal is also added for stability.
## Object Detection, Classification, Localization (ODCL)
Autonomous algorithm is primarily used for ODCL. An emergency manual approach with a human operated user interface is available in case the autonomous algorithm fails.
### ROI
The algorithm autonomously detects regions of interest (ROI) then picks up unique features such as shape, letter, color, location, and orientation of the object. Low memory consumption, high speed, and accuracy are crucial; therefore, computer vision algorithms are preferable over neural network algorithms. OpenCV library is used with Python.

The algorithm is visualized in the flowchart:  ![alt text]([https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png](https://github.com/youssefmohamed0/test/blob/main/flowchart.png) "Logo Title Text 1")

1.	Image is captured then sent to server through mission computer.
2.	Image is smoothed and Canny edge detection algorithm is applied.
3.	Contours are extracted to detect ROI.
4.	ROI is grayscaled then thresholded by OTSU method.
5.	Contours are extracted and shape is detected accordingly (feature extraction method performed better than geometrical methods because of noise in the image.)
6.	Color was determined by calculating average color intensity.
7.	Shape position is calculated by adding the position of the UAV (determined using GPS) to the distance of the shape.
8.	All this information is put into a JSON packet and sent to the interoperability server of AUVSI SUAS.
