---
permalink: /wheelEncoder/
---

# Wheel Encoders
The robot determines where it is in relation to a coordinate frame by using it's wheel encoders. 

# Implementation Overview
This implementation uses the SPI bus to grab "tick" values from the wheel encoder. Tick values relate to the angle at which the wheel encoder is compared to the location of the magnet (seen on the gear). As the gear rotates (as the wheel rotates and the robot moves), the ticks values reported by the wheel encoder will either increase or decrease. The increase or descrease of tick value relates to the increase or decrease of position in the robot. Consequently, the rate at which the ticks values increase or decrease relates to the velocity of the wheel. The individual velocities of the wheel need to be put in terms of the robot as a linear and angular velocity. 

Below is how to take ticket values from the individual wheel encoders and translate them to position and velocity of the differential drive robot. This requies two samples of the wheel encoders, a past and current sample.
```
dt = current_time - prev_sample_time

# Difference in tick values between samples
deltaLeftTicks = current_tick_L - prev_tick_L       
deltaRightTicks = current_tick_R - prev_tick_R 

# Calculate the velocity of each wheel
# DISTANCE_PER_TICK is the distance traveled by tick value (2*pi*wheelradius) / (# of ticks per rev of the wheel)
# (2*pi*wheelradius) is the circumfrance of the wheel
vel_wheel_L = (deltaLeftTicks * DISTANCE_PER_TICK) / dt
vel_wheel_R = (deltaRightTicks * DISTANCE_PER_TICK) / dt

# Determine the robot body's linear and angular velocities
# From the robot's point of view, it can only ever move forward (in the x direction),
#  so velocity in the y direction will always be 0
robot.vx = (vel_wheel_R + vel_wheel_L) / 2
robot.vy = 0;
robot.vth = -(vel_wheel_R - vel_wheel_L) / ROBOT_RADIUS

# Computing the robot's change in position and angle 
delta_x = (robot.vx * cos(robot.th) - robot.vy * sin(robot.th)) * dt
delta_y = (robot.vx * sin(robot.th) + robot.vy * cos(robot.th)) * dt
delta_th = robot.vth * dt

```
![Wheel Encoder](https://github.com/hannabanana96/MPDR_Project/blob/master/assets/images/wheel_encoder_motor.jpg){: .align-center}


Serial Peripheral Interface (SPI) is not natively actived on the Rasp Pi 4, which has up to 6 SPI busses. For our purposes, I chose SPI0. To activate SPI do the following:
`sudo vi /boot/firmware/usercfg.txt` \
And add: `dtparam=spi=on` \
Close the file and in the command line: `pip3 install spidev` \
Then reboot. \
This automatically addes two chip select lines (by default you can run two devices on this SPI line). Next, check that your user has permissions to access the the SPI buss: `ls -l /dev/spi*`
![image-center](https://hannabanana96.github.io/MPDR_Project/assets/images/spi_cmdline.JPG){: .align-center}

# Sources of Error
The are many sources of error with this as wheel encoders can produce false reports when there is wheel slippage. The implementation senses the position of the wheel at the base of the motor on the gearbox instead of the actual wheel. This means that there are several revoluations of the gear per one revolution of the wheel (another potential source of error). 
