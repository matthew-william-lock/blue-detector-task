# blue-detector-task

This repository contains the code for the Blue Detector Task. The task is defined as follows:

> Develop an appropriate system including ROS/Python​ that will prevent operation of a linear actuator if enough color "blue" is detected on an RGB video camera. The delivery should include CAD for the complete system (actuator, camera and anything else relevant). Please carefully review the job description and make sure you are showing your work at your best. Think about the use cases, how we'll read your code, your architecture, and how your solution might break. A single code file is not likely to be sufficient. You can of course make assumptions/simplifications, but state them clearly. 

Solution description:

For this task, I have put together a simulation environment (using the Webots simulator) which will allow me to interact with a robotic platform using ROS2. The robotic platform chosen is the [e-puck mobile robot](https://cyberbotics.com/doc/guide/epuck) and is equipped with a camera. The camera is used to detect the color blue using the developed [ros2_detect_blue](https://github.com/matthew-william-lock/ros2_detect_blue). Please see the pacakge readme for more information on how the color detection works, the three different methods used, and the considerations for each method. 

<img src="https://user-images.githubusercontent.com/53016036/218308194-71ddd6cf-042f-4ae5-9425-f21ca903a957.png" width="100%">

Once the **ros2_detect_blue** packge is running, a boolean message is published to the topic **/blue_detected**. This message can be used to control the linear actuator. I unfortunately did not have time to implement the actuator control and neccessary CAD models within recommended time frame of 2-3 hours. Nevertheless, the approach I would have taken would have been to control a GPIO pin using the **blue_detected** message. This could have been done through
1. A Teensy 4.1 microcontroller connected to the host machine via USB. The Teensy would have been programmed to listen to the **blue_detected** topic using micro-ROS and control the GPIO pin accordingly. 
2. A raspberry pi connected to the host machine via USB. The raspberry pi would have been programmed to listen to the **blue_detected** topic control the GPIO pin accordingly.

Once the GPIO pin is controlled, the actuator can be controlled using a relay. The relay can then be used as a simple control mechanism for the linear actuator as described in [How Do You Control a Linear Actuator with a Relay?](https://www.firgelliauto.com/blogs/tutorials/how-do-you-control-a-linear-actuator-with-a-relay) and [What Type of Actuator Switch Do I Need?](https://www.firgelliauto.com/blogs/tutorials/how-do-you-control-a-linear-actuator-with-a-switch#:~:text=To%20control%20a%20linear%20actuator%2C%20you%20need%20to%20use%20either,relays%20with%20a%20DPDT%20switch.).

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

1. Install the required software needed to run the simulation.

* Ubuntu 22.04

* ROS2 Humble

Install ROS2 Humble following the instructions [here](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html).

* [Webots 2022a](https://github.com/cyberbotics/webots/releases/tag/R2022a)

Run the following commands to install Webots 2022a after downloading the .deb file:

```sh
sudo apt install ./webots_2022a_amd64.deb
```

There is a known cirtificate issue with the Webots installation. To fix this, follow the instructions [here](https://github.com/cyberbotics/webots_ros2/issues/465).

* Numpy
```sh
sudo apt-get install python3-numpy
```

* cv2

```bash
sudo apt-get install python3-opencv
```

2. Clone and build the necessary packages.

```bash
git clone --recurse-submodules -j8 https://github.com/matthew-william-lock/blue-detector-task
```

Move the ROS2 packages into the `src` folder of your ROS2 workspace:
```sh
cp -r blue-detector-task/src/ros2_detect_blue ~/ros2_ws/src/
cp -r blue-detector-task/src/ros2_numpy ~/ros2_ws/src/
cp -r blue-detector-task/src/webots_ros2 ~/ros2_ws/src/
cp -r blue-detector-task/src/webots_ros2_jetbot ~/ros2_ws/src/
```

Build the ROS2 packages:
```sh
cd ~/ros2_ws
rosdep install -i --from-path src --rosdistro $ROS_DISTRO -y
colcon build 
```

Source the ROS2 workspace:

```sh
source ~/ros2_ws/install/local_setup.bash
```

Move the Webots world file into the Webots projects folder:

```sh
cp blue-detector-task/src/worlds/epuck_world.wbt ~/ros2_ws/install/webots_ros2_epuck/share/webots_ros2_epuck/worlds
```

<!-- USAGE EXAMPLES -->
## Usage

To start the Webots simulation, run the following command:

```sh
ros2 launch webots_ros2_epuck robot_launch.py
```

To control the robot platform, we make use of the [teleop_twist_keyboard](https://github.com/ros2/teleop_twist_keyboard) package. To start the teleop_twist_keyboard node, run the following command:

```sh
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

The robot can then be controlled using the following keys as shown in the table below:

| Key | Action |
| --- | --- |
| i | Move forward |
| j | Turn left |
| l | Turn right |
| m | Move backwards |

### Detecting Blue

To start the color detection node, run the following command:

```bash
ros2 run ros2_detect_blue detect_blue --ros-args -p detection_method:=0
```

This will publish the **blue_detected** message to the topic **/blue_detected**.

> Please see the full documentation for the **ros2_detect_blue** package [here](https://github.com/matthew-william-lock/ros2_detect_blue).


## Experimental Results

Shown below are some experimental results for the different detection methods.

> Again, please see documentation for details on the different detection methods and explanations of the shortcomings of each method.

### Simple Thresholding
https://user-images.githubusercontent.com/53016036/218309110-8b128d47-b508-49e8-b32b-7e9dda9c09ab.mp4

As described in the documentation, the simple thresholding method is not very robust. In this simulation environment, the robot is able to detect the blue object but is unable to distinguish between the blue object and the sky. This is due to the fact that the sky is also blue and the thresholding method is unable to distinguish between the two. This would not be an ideal solution for scenarios where the camera will have the sky in view.

### Thresholding with K-Means Clustering

https://user-images.githubusercontent.com/53016036/218309121-dec40923-91dd-4433-978d-a8bf440fc410.mp4

This video shows the results of the thresholding with K-Means clustering method. Clusters with centroids in the top 10% of the image are discarded. This method is able to distinguish between the blue object and the sky. This method is more robust than the simple thresholding method but is still not a good general solution. For example, if the blue object was placed against the blue sky, the robot would not be able to detect the blue object.

### Thresholding with K-Means Clustering and Coverage Thresholding

https://user-images.githubusercontent.com/53016036/218309144-ca885ada-20e5-4aa4-86ea-90c0acda8cf7.mp4

This video shows the results of the thresholding with K-Means clustering and coverage thresholding method. The idea here is to detect object that are close to the camera by determining the coverage of the cluster. The video shows that this method successfully detects the blue object only when the robot gets close. Still, this method is not a good general solution as you would likely want to specify an exact object size and distance from the camera. This could be done with a stereo camera but this is not implemented in this simulation.

