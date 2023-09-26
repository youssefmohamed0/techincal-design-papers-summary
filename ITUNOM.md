# ITÜNOM
The UAV was designed to attain completion in all missions. It is able to fly and land autonomously, follow waypoints with high accuracy, carry and drop a UGV, generate a map from captured images, search for standard and emergent objects and avoid both stationary and moving obstacles.
## Initial Decisions
The first decision was selecting a vehicle type. A fixed wing vehicle type was chosen, and other structural aspects were focused on to provide sufficient stability for the autonomous detection and mapping missions and maneuverability for precise and time efficient flight.	
## Imaging Systems
The choice of camera was based on certain requirements. The camera is required to support high resolution to be able to capture images of small ground objects from a high flight position. Flight speed is also taken into consideration which is why a sufficiently high shutter speed is necessary to prevent capturing blurry images during high-speed flight. The camera must also support an application program interface (API). **Sony 𝛼6000** camera has been chosen for the mission. A gimbal is also added for stability.
## Object Detection, Classification, Localization (ODCL)
Autonomous algorithms are primarily used for ODCL. An emergency manual approach with a human operated user interface is available in case the autonomous algorithm fails.
### ROI
The algorithm autonomously detects regions of interest (ROI) then picks up unique features such as shape, letter, color, location, and orientation of the object. Low memory consumption, high speed, and accuracy are crucial; therefore, computer vision algorithms are preferable over neural network algorithms. **OpenCV** library is used with **Python**.

The algorithm is visualized in the flowchart:

![alt text](https://github.com/youssefmohamed0/test/blob/main/images/itunom%20pictures/Picture3.png)

1.	Image is captured then sent to server through mission computer.
2.	Image is smoothed and Canny edge detection algorithm is applied.
3.	Contours are extracted to detect ROI.
4.	ROI is grayscaled then thresholded by **OTSU** method.
5.	Contours are extracted and shape is detected accordingly (feature extraction method performed better than geometrical methods because of noise in the image.)
6.	Color was determined by calculating average color intensity.
7.	Shape position is calculated by adding the position of the UAV (determined using GPS) to the distance of the shape.
8.	All this information is put into a JSON packet and sent to the interoperability server of AUVSI SUAS.

The algorithmic process is assembled using **ROS** which supports threading.

A user interface is designed for manual ODCL to be used to compensate for the failure of the autonomous system.
## Mapping
Photos will be taken by UAV, and a map will be created using image stitching algorithms. 
The number of photos used in merging will be calculated using the following equation.

![Alt text](https://github.com/youssefmohamed0/test/blob/main/images/itunom%20pictures/Picture2.png)

From the formula, it was calculated that approximately 42 photos should be taken at an altitude of 1200 feet from the center of the map.

The quality of the photos starts to decrease after 30 photos, so a 3rd party software is used. **Ptgui PRO** is chosen for its correction and masking features and its overall more professional results.
## Autopilot
 A reliable autopilot is necessary for a successful and stable autonomous flight. **Pixhawk Cube Orange** has been chosen for its reliability and many more capabilities such as:

*	Fully autonomous flight mode that can execute a fully scripted flight.
*	Soft mount.
*	Peripherals compatible with most sensors.
*	Various communication protocols such as USB, CAN, I2C, and SPI.
*	Autotune feature that improves flight performance and stability.
*	ROS compatibility.
*	Auxiliary outputs that can control payload mechanism or camera.

Along with Pixhawk, **Ardupilot** firmware is also used for its various configurations and customizability.
## Obstacle Avoidance
### Static Obstacles
The obstacle avoidance algorithm consists of two main steps, preprocessing and path planning. cartesian coordinates of the waypoints and obstacles are used for calculations. Paths between each waypoint are considered as rectangles instead of lines. Obstacles are checked and obstacles which have an intersection with the path rectangle are taken into consideration.

With this approach if the UAV finds an obstacle on its path, the fight plan will be updated, marking two new possible sub paths around each side of the obstacle. The shortest path is chosen to maximize efficiency and minimize change in the original path. A small detour is applied to obstacles that only occupy a small part of the rectangular path. 
### Dynamic Obstacles
In order to avoid moving obstacles, telemetry data of opposing aircraft is retrieved from the interoperability system every second. Among these data, the position, speed, and altitude.

![Alt text](https://github.com/youssefmohamed0/test/blob/main/images/itunom%20pictures/Picture4.png)
## Communications
The communication infrastructure is built upon four legs: telemetry data transfer to GCS, manual control, ODCL data transfer over Wi-Fi, and real-time image transfer.

To avoid interference with other operating frequencies, **RFD900X** is used in the ground station and in the air vehicle to transfer telemetry data. RFD900X has a frequency hopping spread spectrum, which blocks interference and can transfer data at 200Kbps, and makes the data transfer stable using its error correction routines for MAVLINK protocol.

Futaba T14SG radio controller is used in case of an emergency. It can communicate with the on-board Futaba R7008SB FASSTest receiver on 2.4 GHz bandwidth.
## Refrence
[ITÜNOM Techincal Design Paper](https://drive.google.com/file/d/1Hu7DjxAuMirIuY8a4bGs_LTfQ1pByS0L/view?usp=sharing)
