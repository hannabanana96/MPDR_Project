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

## Future Chassis Improvements
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

# Path Following

# Obstacle Avoidance

# GPS Live Tracking

# Path Creation


# Web Page

