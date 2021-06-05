---
permalink: /gps/
title: GPS Sensor
---

# Overview
The GPS sensor is used for live tracking of the robot's location, and eventually would help with global positioning. 

# Hardware Setup
The GPS used for this project is the [NEO-M9N, SMA](https://www.u-blox.com/en/product/neo-m9n-module) via [Sparkfun breakout board](https://www.sparkfun.com/products/17285). This is accompanied by a [GNSS Multi-Band Magnetic Mount Antenna](https://www.sparkfun.com/products/15192). This GPS sensor has several different communication busses (UART & I2C). UART was chosen to match a pre-made ROS package. The GPS can be powered either by either 3V3 or 5V. 

To enable the UART pins on the Pi:
* `sudo vi /boot/firmware/usercfg.txt`
* Add/change `enable_uart=1`
* Reboot to activate

The primary UART for the Pi 4 is the mini UART (UART0) --> /dev/ttyS0 or /dev/serial0 (/dev/serial0 is a symbolic link to /dev/ttyS0). UART0 is assoicatd with pins 8 & 10 (GPIO 14 & 15) on the Pi.

[Raspberry Pi UART page for more information](https://www.raspberrypi.org/documentation/configuration/uart.md)

Connect the GPS RX to Pi TX, GPS TX to Pi RX. 

*This UART is connected to the UART console. This needs to be disabled if the GPS needs to be connected to the Pi on startup, otherwise the Pi will not start! This was not implemented due to time constraints. A suggestion is to try have the GPS connect to a different serial port on the Pi that isn't connected to the UART console (I'm not sure if the console is connected to all of the UART busses or just UART0). 

# Software Setup
This sensor can be used with a pre-made ROS package created to support u-blox GPS receivers. Only the serial configuration of the driver has been tested.
* ROS package: [ublox](https://github.com/KumarRobotics/ublox)
* Git clone in into the `catkin_ws/src` directory to create a GPS package

In the ublox ROS package, set the serial port to ttyS0. Easiest way to do this is to `cd` into the `ublox` package, then `grep -rn "tty*"` and modify accordingly.

Update `launch/ublox_device.launch` to point to the corresponding parameter file. I found that the `zed_f9p` parameter file worked for our purposes. 

![GPS Launch Parameters](https://hannabanana96.github.io/MPDR_Project/assets/images/gps_param.JPG){: .align-center}

To launch the GPS sensors:
`roslaunch ublox_gps ublox_device.launch`

This should create a rostopic `fix` that can be echo'ed to confirm that the GPS sensor is transmitting data.
