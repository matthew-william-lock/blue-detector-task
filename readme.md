# blue-detector-task

This repository contains the code for the Blue Detector Task. The task is defined as follows:

> Develop an appropriate system including ROS/Python​ that will prevent operation of a linear actuator if enough color "blue" is detected on an RGB video camera. The delivery should include CAD for the complete system (actuator, camera and anything else relevant). Please carefully review the job description and make sure you are showing your work at your best. Think about the use cases, how we'll read your code, your architecture, and how your solution might break. A single code file is not likely to be sufficient. You can of course make assumptions/simplifications, but state them clearly. 

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

* Ubuntu 22.04

* ROS2 Humble

Install ROS2 Humble following the instructions [here](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html).

* [Webots R2023a](https://github.com/cyberbotics/webots/releases/tag/R2023a)

Run the following commands to install Webots 2022a after downloading the .deb file:

```sh
sudo apt install ./webots_2023a_amd64.deb
```

* Webots ROS2 package

For this project, the webots_ros2 package was built from source, with the specific commit found in the ```webots_ros2``` submodule in the ```src``` folder. 

Follow the instructions [here](https://github.com/cyberbotics/webots_ros2/wiki/Linux-Installation-Guide) to install the Webots ROS2 package into your ROS2 workspace, but be be sure to use the branch 