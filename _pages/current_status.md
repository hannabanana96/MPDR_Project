---
permalink: /current_status/
title: Current Status of Robot Delivery System
---

# Chassis Upgrade
To help with development, the sensors were moved out of the container on to a laser cut acrylic sheet. This enabled easier debugging of the sensors connections and removal of the pi/sensors. The acrylic sheet is held up by custom 3D printed mounts that connect to the metal bars of the chassis. Files for the 3D printed and lasercut parts can be found [here](https://hannabanana96.github.io/MPDR_Project/solidworks/). 

Previous Chassis:
![Original Robot](https://hannabanana96.github.io/MPDR_Project/assets/images/original_robot.jpg){: .align-center}

Current Chassis: 
![New Robot](https://hannabanana96.github.io/MPDR_Project/assets/images/new_robot.jpg){: .align-center}

## Future Chassis Improvements/Suggestions
* Currently there are only bumpers on the front of the robot. It would be beneficial to add bumpers on the back as well 
* The electronics will requires a more secure enclosure when development begins to come to a close
* There would need to be a sturdy platform so that packages can be placed on the robot

# Manual Controls
There were two implementations of manual controls: using a joystick on a webpage, and using a ROS package [teleop_twist_keyboard](http://wiki.ros.org/teleop_twist_keyboard). 

## Teleop Twist Keyboard
The ROS package [teleop_twist_keyboard](http://wiki.ros.org/teleop_twist_keyboard) uses the keys [U I O J K L M , .] to control the robot. This package translates these keys into Twist messages published in the `/cmd_vel` topic. \
To run:
* Check that the motor controller is connected to the Pi. 
* Start a `roscore`
* In a new terminal: `rosrun teleop_twist_keyboard teleop_twist_keyboard.py` \
*Teleop can be intalled by: `sudo apt-get install ros-noetic-teleop-twist-keyboard`

## Webpage Joystick
On the Admin page of the Delivery System's Web App, there is joystick that can be used to manually control the motors via the `/cmd_vel` topic. To publish ROS topics from a webpage, the [rosbridge_suite](http://wiki.ros.org/rosbridge_suite) ROS package needs to be installed. 

To run:
* Launch a ROS websocket: `roslaunch rosbridger_server rosbridge_websocket.launch`
* Navigate to the directory of the Web App (wherever the index.html or index.php) is located.
* If the index.* file is a .php extension, then start a php server: `php -S localhost:8000`
* If the index.* file is an html extension, then start a http server: `python3 -m http.server`
* Go to a web browser on the same machine (things that are set to localhost are only viewable on the same machine), and go to`localhost:8000`. This should take you to the main Web page
* Navigate to the Admin page
* Move the joystick on the wepage, and `rostopic echo /cmd_vel` in a terminal (checking to see if command velocities are being published by the web page)
* Check that the motor controller is connected to the Pi.
* `rosrun mpdr encoders.py` to run motor controller - robot will now move!

![Webpage Joystick](https://hannabanana96.github.io/MPDR_Project/assets/images/manual_controls.png){: .align-center}

<video width="480" height="320" controls="controls">
  <source src="https://hannabanana96.github.io/MPDR_Project/assets/video/guiToRobot_liveDemo.mp4" type="video/mp4">
</video>

<iframe src="https://drive.google.com/file/d/160EMM-p5eYeebYeJ8ykyQeFmLtIdVe65/view" frameborder="0" allowfullscreen></iframe>

## Future Manual Controls Improvements/Suggestions
I recommend removing the joystick from the Web App, and strictly using the ROS teleop package. Using the web page to control the robot is limiting such that it can only be used where there is network connectivity. The ROS teleop package fulfilled our goal of being able to manually control the movement of the robot for testing purposes.

# Path Following and Obstacle Avoidance
The path following implementation uses the [ROS navigation stack](http://wiki.ros.org/navigation) and is based off of tutorials provided in the ROS Wiki. There are two sensors needed for this [Lidar](https://hannabanana96.github.io/MPDR_Project/lidar/), and [WheelEncoder](https://hannabanana96.github.io/MPDR_Project/wheelEncoder/). Following the instructions on their respective pages for hardware/software bring up.

1. Following the [Configuring and Using the Navigation Stack tutorials](http://wiki.ros.org/navigation/Tutorials/), the first step is setting up the transform between the lidar and the base_link of the robot. If you are unfamiliar with transforms, the "Setting up the Lidar Transform" gives some explanation. 

[Setting up the Lidar Transform Tutorial](http://wiki.ros.org/navigation/Tutorials/RobotSetup/TF). 

Our implementation of the lidar transform is found in our mpdr package, `lidar_tf.py`. Our implementation of the lidar also includes a scan_filter that filters out the area on the back of the robot where the touchscreen sits (so that the lidar doesn't see an "object" in the robot body. This is found in the mpdr package, `scan_filter.py`. 

2. The next tutorial is to ensure the wheel encoders are producing Odometry information via the [Publishing Odometry Information over ROS tutorial](http://wiki.ros.org/navigation/Tutorials/RobotSetup/Odom). Odometry is the use of data from motion sensors to estimate the change in position over time. In the mpdr package, the [`encoders.py`](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/src/encoders.py) script handles the creation of the odometry broadcast and creation of an odometry message topic.

Tuning/checking the odometry is very important! I recommend following the [Basic Navigation Tuning Guide](http://wiki.ros.org/navigation/Tutorials/Navigation%20Tuning%20Guide) to help tun the odometry. I also found this article ["Deploying on Mars: Rock Solid Odometry for Wheel Robots"](https://www.freedomrobotics.ai/blog/tuning-odometry-for-wheeled-robots) very useful as well.

3. The next step is to follow the [Setup and Configuration of the Navigation Stack on a Robot](https://www.freedomrobotics.ai/blog/tuning-odometry-for-wheeled-robots) tutorial. This tutorial creates several files, these are current implementation's versions:
* [`robot_configuration.launch`](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/launch/robot_configuration.launch)
* [`costmap_common_params.yaml`](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/config/costmap_common_params.yaml)
* [`global_costmap_params.yaml`](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/config/global_costmap_params.yaml)
* [`local_costmap_params.yaml`](http://wiki.ros.org/navigation/Tutorials/RobotSetup)
* [`base_local_planner_params.yaml`](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/config/base_local_planner_params.yaml)
* [`move_base.launch`](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/launch/robot_configuration.launch)

The ROS navigation stack tutorial requies a map for move_base.launch (launches the ROS navigation stack). An empty map and be utilized by adding an all white pgm file. Alternatively, a map can be created of your surrouding area with the lidar and the [slam_gmapping](http://wiki.ros.org/navigation/Tutorials/RobotSetup) ROS package. Follow the previous slam_gmapping tutorial to create a map for the navigation stack. 

In move_base.launch (launches the navigation stack), it uses AMCL as the localization alogrithm. The robot is a differential drive robot, so makes ure to change the AMCL launch from amcl_omni.launch to amcl_diff.launch.

## Run Navigation Stack
Now you should be able to run the navigation stack:
* `roslaunch mpdr robot_configuration.launch`
* `roslaunch mpdr move_base.launch`

With the two launch files running, you can pull up rviz (`rviz rviz`) and give the robot a navigation goal. Some topics that would be useful to add in your rviz window (Add, then Add by topic):
* /<odometry topic> (whatever you odometry topic is called)
* /amcl_pose -- shows where the robot thinks it is 
* /scan -- the lidar
* the local or global costmap
  
Before you give the robot a simple goal in rviz, you will need to give it an initial pose estimate (the initial pose and simple goal buttons in rviz next to each other near the top right of the rviz window). 

## Obstacle Avoidance
With the navigation stack running, the robot will automatically attempt to avoid obstacles within its path. This feature was not thoroughly investigated. We were able to successfully navigate around a single object directly in line of sight of the initial position of the robot.

# Path Following Improvements/Suggestions/Notes
* The robot needs to be able to operate outdoors, so this might include loading in an "empty" map. We tried actively running slam_gmapping to create a real-time map while running robot_configuration.launch (launches sensors) and move_base.launch (launches navigation stack), but it was too computationaly expensive for the raspberry pi.
* Overtime the wheel encoder incur a decent amount of drift, as seen in the [Wheel Encoders](https://hannabanana96.github.io/MPDR_Project/wheelEncoder/) setup page. This indicates that the addition of another position sensor would be required to give more accurate position odometry information. Sensor fusion and the use of an extended kalman filter could greatly assist with this, and are included in the [ros_localization](http://docs.ros.org/en/melodic/api/robot_localization/html/index.html) ROS package. My attemps to fuse the IMU sensor and the Wheel Encoder was unsuccesful. A lot of the reading I conducted indicated that setting the covariance matrix for each of the sensor messages was important (the covariance matrices indicate to the kalman filter how noisy or how trustworthy the incoming message is).
* The end goal of the system is to eventually navigate via GPS coordinates. Therefore the incorporation of a GPS sensor into the Odometry frame is necessary. The [ros_localization](http://docs.ros.org/en/melodic/api/robot_localization/html/index.html) ROS package has a [navsat_transform_node](http://docs.ros.org/en/melodic/api/robot_localization/html/integrating_gps.html) could be used for just that. My suscipion is that another node/package would be needed to transform GPS waypoints into coordinates that the robot can follow (the navsat_transform_node only updates the robot's position, doesn't have anything to do with goals). 


# GPS Live Tracking

# Path Creation


# Web Page

