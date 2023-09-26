# UTAT
The design of the UAS is fixed wing. The UAS carries a ground vehicle payload. The UAS is designed to be able to accomplish all tasks successfully.
## Imaging Systems
### Camera
The camera used is a **Teledyne-Dalsa Genie NanoXL C5100**. The camera’s primary connection method is ethernet with support for PoE. It has a resolution of 5120x5120 with a maximum of 20.1 frames per second (fps).
### Imaging Software
The camera is connected to an onboard **Odroid XU3 SBC** “companion” computer which runs the camera driver software. The computer is also connected via an FTDI USB cable to the telemetry port of a **PixHawk Cube 2.1** flight controller. This allows the camera to take photos at approximately 2 fps.

The camera driver software interfaces with the Pixhawk and records the exact location of the UAS at the time of image capture, which is sent to the onboard computer.
## Object Detection, Classification, Localization
The ODCL pipeline consists of several independent but integrated computer vision modules that each perform a key function.

First, a 4096x4096 aerial image is sent to the ground station. A target detection library uses **YOLOv3** to detect any targets then outputs cropped images of the target and their relative position in pixel coordinates. A geolocation module calculates the GPS coordinates of the target.

Several smaller modules utilizing OpenCV are then each called in a master Python script.

*	A color detection module that uses **K-means clustering** to isolate the colors and then match them to colors from a pre-defined list. The same module is used to identify the color of the letter inside the shape.
*	A shape detector module that masks the shape and classifies it based on contour of the shape.
*	A character recognition module that masks letters and uses **Tesseract optical character recognition engine** to identify the characters.

The image along with a Json file containing the identified image characteristics are outputted. The last step of the ODLC pipeline is a **PyQt4** manual processing GUI that allows a human operator to correct the classification if necessary. A script automatically uploads the image and metadata to the interoperability server.
## Mapping
Aerial images are copied off the companion computer, and the algorithm uses OpenCV to stitch groups of images together. The algorithm relies on feature matching to detect overlaps in images.
## Autopilot
The UAS uses a **Pixhawk2.1 cube** flight controller that runs **PX4** autopilot firmware, which supports the ability to switch between manual and autonomous flight, direct interfacing with cameras using **Mavlink** protocol, flight logging, and Return-To-Home. The main reason for choosing PX4 is support of **MavSDK**, which allows control of the UAS using object-oriented programming rather than directly sending messages using the Mavlink protocol.
Another key feature of the PX4 autopilot is the support for **QGroundControl** to monitor, log, analyze and control the UAS if necessary.

![Alt text](https://github.com/youssefmohamed0/test/blob/main/images/Utat%20pictures/Picture1.png)
## Obstacle Avoidance

The obstacle avoidance system receives information about obstacles and other aircrafts’ positions from the interoperability server and feeds it into the onboard computer to from a 2D model of the airspace. The obstacle avoidance system combines this model with a planner to produce sets of waypoints.

The UAS continuously replans in flight through a process known as **model predictive control** (MPC). Planning is done by repeatedly generating sets of waypoints.

The planner outputs a path consisting of changes in heading and waypoints. The planner first selects a set of random plan parameters. Each of these plans is then simulated and ranked according to the cross-entropy values, discarding plans that get too close to obstacles or the simulated future positions of other aircraft. The remaining top k plans are taken and are fit with a gaussian distribution to their parameter vectors.

A new set of plans is generated from this distribution. This process is repeated until convergence.
## Communication
A visual overview of the entire communications system is shown in the following figure: 

![Alt text](https://github.com/youssefmohamed0/test/blob/main/images/Utat%20pictures/Picture2.png)

Telemetry: RFD900X, 3 dBi half wave dipole
*	Transmission of flight data to the GCS for monitoring, uploading of waypoints
*	Max range of over 40km, frequency of 900 MHz and Tx power of 1.0 W

RC: Futaba T10J Dragonlink Transmitter with a 433 MHz omni-antenna at 3 dBi, Dragonlink Receiver at 433MHz with an omni-antenna Rx sensitivity of -108 dBm
*	Manual control of UAV in emergencies, flight mode changes, immediate flight termination
*	Range of over 60km, frequency of 433 MHz and Tx power of 1.0 W

## Refrence
[UTAT Techincal Design Paper](https://drive.google.com/file/d/1LLd5Tj4B4QE6Nst0W2pFzkBIK8xRjTtS/view?usp=sharing)
