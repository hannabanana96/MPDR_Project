---
permalink: /bumpers/
title: Bumpers
---

# Overview
These bumpers use a custom 3D printed frame with [sub micro limit switch level arms (SPDT snap action)](https://www.amazon.com/Switch-Action-250VAC-Terminals-Momentary/dp/B0778GPX39), and a custome ROS package.

![Robot Bumpers](https://hannabanana96.github.io/MPDR_Project/assets/images/robot_bumpers.jpg){: .align-center}

# Hardware Setup

The GND of the switches are all connected to a GND on the Pi. Each of the switches is then connected to a GPIO pin on the Pi (current implementation connects to GPIO 17 and GPIO 27 to the two switches that are being used)

# Software Setup
Assuming the [mpdr](https://github.com/hannabanana96/MPDR_Masters/tree/master/mpdr) package and ROS are installed, the software related to the bumpers can be found in [`bumpers.py`](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/src/bumpers.py).
To test the bumpers:
* Start a `roscore`
* In another terminal: `rosrun mpdr bumpers.py`

You should be able to `rostopic echo /bump` to see the resulting messages: 
> 1 = bumper has been hit
> 0 = bumper has not been hit
