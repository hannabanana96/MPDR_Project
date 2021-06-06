---
permalink: /ros_setup/
title: ROS Setup Guide
---

# Two Pi's talking on ROS
First, the two Pi's must be on the same network in order to communicate with each other. To check if the Pi's (or any two devices running ROS on the same network) can properly communicate with each other, follow the [ROS tutorial on Full Connectivity](http://wiki.ros.org/ROS/NetworkSetup), checking both `ping` and `netcat`.

We'll call the two Pi's: robot_pi, and other_pi.
In the ~/.bashrc file of the robot_pi, add the following:
```
export ROS_IP=<public IP of the robot_pi>
export ROS_MASTER_URI=http://<public IP of robot_pi>:11311
```
In the ~/.bashrc file of the other_pi, add the following:
```
export ROS_IP=<public IP of the other_pi>
export ROS_MASTER_URI=http://<public IP of robot_pi>:11311
```
This indicates to ROS that the robot_pi is the master. \
Launch a ROS score on the master (`roscore`) and make sure the other_pi can see the topics (`rostopic list`)


