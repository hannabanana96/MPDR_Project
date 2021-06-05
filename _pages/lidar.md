---
permalink: /lidar/
---

The lidar is used for localization, mapping, and nearby obstacle detection (obstacles that the robot needs to navigate around).

# Hardware and Software Setup
The lidar used in this project is the [Slamtec RPlidar S1](https://www.slamtec.com/en/Lidar/S1), chosen for its outdoor capabilities. The lidar is connected to the Pi via two USB ports (power and data). Pre-made ROS packages are used:
  * ROS package: [rplidar_ros](https://github.com/Slamtec/rplidar_ros)
  * ROS Wiki: [rplidar](http://wiki.ros.org/rplidar)
  * Clone the package into the `catkin_ws/src` directory to create the lidar package
  * To launch the S1 lidar package: `roslaunch rplidar_ros rplidar_s1.launch`
This will launch the lidar package, and start a topic with the lidar scans `/scan`.

To check that the ROS is receiving the lidar data, open RVIZ and pull up the lidar data
* Start a `roscore`
* In another window: `rviz rviz`
* In rviz, select `Add` then `Add by topic`, and select `/scan`. You should now see the lidar scans in the rviz window

** Add a lidar picture from RVIZ **

