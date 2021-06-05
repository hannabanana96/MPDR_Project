---
permalink: /imu/
---

# Overview
The IMU sensor for this project is the [BNO085](https://www.ceva-dsp.com/wp-content/uploads/2019/10/BNO080_085-Datasheet.pdf) on an [Adafruit breakout board](https://learn.adafruit.com/adafruit-9-dof-orientation-imu-fusion-breakout-bno085). This senors has 9 degrees of freedom, including a triaxial accelerometer, a triaxial gyroscope, and a magnetometer. 


# Setup
## Hardware Setup
This IMU sensor uses I2C for communication. Connect the IMU sensor to the Pi's ISC1 bus. I2C and the clock rate need to be set for the Pi.
* `cd /boot/firmware/usercfg.txt`
* Add `dtparam=i2c_arm=on` 
* Add `dtparam=i2c_arm_baudrate=400000`
* Reboot

To test if the IMU sensor is connected via I2C to the Pi:
* `sudo apt install -y i2c-tools`
* `sudo i2cdetect -y 1`
You should see a device and its address if there is a successful connection

## Software Setup
This sensors uses a pre-made ROS package.

* ROS Package: [ros_bno08x](https://github.com/GAVLab/ros_bno08x)
* Git clone the package into `catkin_ws/src` to create an IMU package.

The I2C address and the clock rate in `talker.py` need to be updated to match the values in the Hardware Setup section.

To launch the IMU sensor: \
`roslaunch rox_bno08x bno08x.launch` \
This should create a rostopic that can be echo'ed to confirm the sensors is outputting data (`/bno08x/raw`)
