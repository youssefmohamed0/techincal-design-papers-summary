# Atlas
The UAV is designed to be able to complete all mission requirements. It is able to complete all waypoints autonomously while avoiding obstacles. The UAV will complete a UGV drop task, ODCL tasks, and a mapping by capturing images of the ground beneath it. The UAV has enough Li-Po batteries to complete the task as proven by the several tests performed prior to the competition.
## Camera Choice
To be able to capture high speed and high altitude, high resolution and shutter speed are crucial. Sony A600 was chosen as the capture camera. This camera supports Gphoto2 library which is needed to trigger the camera from companion computer. A gimbal is added for stability.
## Object Detection, Classification, Localization (ODCL)
The autonomous display system uses NVIDIA Xavier NX due to its low power consumption and high performance. The algorithm has been developed with deep learning methods to detect and classify targets on images from the camera.

An error limit has been set for the detection process. Targets with low trust value are transferred directly to the manual detection system.
### YOLO
A CNN based yolo algorithm is chosen for its high processing speed and accuracy. A dataset containing over 40,000 photos has been created by team members. 

The model has been trained by processing through 24 convolutional, 3 pooling, and 2 output layers, using Non-maximum Suppression Algorithm to prevent multiple detections of a single object. 

Captured images are processed then a bounding box with the class label is drawn around the detected objects. Using pixel values of the bounding box coordinates, the shape is cropped, and its color is determined using functions in the OpenCV library. color determination was facilitated by converting from BGR color space to HSV. The desired color is obtained by masking the image.

From the cropped image, letters are detected using YOLO. An accuracy rate of 97% is achieved.
### Localization
To find the location of the detected object, first the size of the object is obtained using the following equation.

![Alt text](https://github.com/youssefmohamed0/test/blob/main/images/Atlas%20pictures/Picture1.png)

The distance between the center of the object and the vehicle is found and converted from pixel values to values in meter. The drone’s relative angle to the object is determined, and then the absolute bearing is calculated based on the drone and object’s positions. The absolute bearing and drone’s location are used to compute the geodesic distance using the GIS Python library, GeoPy.
### Manual Object Detection
A manual ODCL interface for labeling objects has been developed using C# and WPF. The user interface is able to run on three different computers, making the labeling process much faster. 

![Alt text](https://github.com/youssefmohamed0/test/blob/main/images/Atlas%20pictures/Picture2.png)

## Mapping
GPhoto2 is used to control the camera. Captured photos are saved on the Xavier NX. During the saving, the location info is obtained using the Pixhawk Cube Black and embedded into the image through a program developed by the team. Images containing GPS metadata are sent to the ground control station for mapping.

Using geotagged photos and flight logs, the mapping process is performed on the Agisoft software. WGS 84 Web Mercator is selected as the projection method. With these setting a high-quality image can be obtained in 6 minutes.
## UGV System
Pixhawk2.4.8, which includes an internal magnetometer, gyroscope, and accelerometer, is used for the autopilot of the UGV. An external GPS module is added to the system to increase location accuracy. A Li-Po battery is preferd as the power source of the UGV.

## UAV Autopilot
The UAV performs autonomous flight through the autopilot card, IMUs, and autopilot software. The autopilot card Pixhawk2.1 Cube Black was chosen because it is highly tested, reliable, open-source system, affordable and is compatible with most common sensors. Equipped with a 32-bit STM32F427 microprocessor with ARM Cortex-M4F core, this card has three redundant IMUs, 2 redundant barometers, and 14 PWM outputs.

Ardupilot4.1.3 autopilot software was chosen for its developer support, compatibility, systematic updates, and support for various flight modes. It also includes an autotune mode.

Mission Planner is used as the ground control station software to control flight and intervene when necessary.
## Obstacle Avoidance
A* algorithm was used for path planning since it proved more capable of finding the shortest path.

Obstacle coordinates are taken from the interop server, and the latitude and longitude are converted into cartesian coordinates to be used in the obstacle avoidance algorithm. A new flight path is generated and sent to the ground control station.
## Communications
Telemetry module RFD900x with dipole antenna is used to transfer status of UAV and flight data to the ground control station. The main reason for choosing this module is its secure end-to-end encryption (128-bit AES) property. 3DR V5 915 MHz telemetry package was selected for communication between UGV and its GCS.

Radio control communication is established between the drone and the safety pilot to change the flight modes and provide control in non-autonomous modes. The RC System that the team uses is Radiolink AT9S Pro 10 Channel RC Transmitter with R9DC 9 Channel Receiver.

Wi-Fi communication through Nanostation Loco5 AC is used to transfer data apart from telemetry.

An antenna tracking system using Pixhawk2.4.8 is being developed by the team members.
## Refrence
[Atlas Technical Design Paper](https://drive.google.com/file/d/1XPeUINckVp9LRkIWNq1cuhNbRoKhDuqI/view?usp=sharing)
