---
permalink: /ros_setup
title: ROS Setup Guide
---

# Two Pi's talking on ROS
First, the two Pi's must be on the same network in order to communicate with each other. To check this, make each Pi can `ping` the other. We'll call the two Pi's: MASTER_Pi, and ONBOARD_Pi.
In the ~/.bashrc file of the MASTER_Pi, add the following:
```
export ROS_IP=<public IP of the MASTER_Pi>
export ROS_MASTER_URI=http://<public IP of MASTER_Pi>:11311
```
In the ~/.bashrc file of the ONBOARD_Pi, add the following:
```
export ROS_IP=<public IP of the ONBOARD_Pi>
export ROS_MASTER_URI=http://<public IP of MASTER_Pi>:11311
```
This indicates to ROS that the MASTER_Pi is the master. 
Launch a ROS score on the master (MASTER_PI) and make sure the ONBOARD_Pi can see the topics (`rostopic list`)

