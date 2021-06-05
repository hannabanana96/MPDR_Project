---
permalink: /motor_controller/
---

# Overview
This project reuses two motorized wheelchair brushed motors. The motors came with 24V electric brakes that have since been removed so that the wheel enocders could be used. The current motor controller was passed down as part of the previous version of the project - 30A 7V-35V SmartDrive DC Motor Driver (2 Channels). Custom motor controller code was written to convert a desired linear and angular robot body velocities into a serial packet for the motor controller (identifying direction and motor speed for each of the motors). 

![Wheel with E-brake](https://hannabanana96.github.io/MPDR_Project/assets/images/wheel_with_ebrake_smaller.jpg){: .align-center}

# Hardware Setup
The motor controller has two channels that are connected to the motors. The two channels are labeled MLA/MLB for the left motor, and MRA/MRB for the right motor. In the picture below, the yellow and gray wires are for the left motor, blue and purple wires are for the right motor.

![Motor Wiring](https://hannabanana96.github.io/MPDR_Project/assets/images/motor_pinout.jpg){: .align-center}

Because UART0 is being used by the GPS sensors, another UART bus needs to be enabled. 
* `cd /boot/firmware/usercfg.txt`
* Add `dtoverlay=uart2
* Reboot
* `ls /dev/ttyAMA*` and there should now be an additional active bus (ttyAMA1)

Connect GND and INT1 from the motor controller to GND and GPIO 27 (TXD2) on the Pi. 

# Software Setup
The custom motor controller code can be found in the [mpdr](https://github.com/hannabanana96/MPDR_Masters/tree/master/mpdr) package, in [mtr_ctrl.py](https://github.com/hannabanana96/MPDR_Masters/blob/master/mpdr/src/mtr_ctrl.py). This converts a ROS Twist command (linear and angular velocity message) to a direction and speed for each of the motors connected to the motor controller. This code opens a serial port on the designated port `/dev/ttyAMA1/` with a baudrate of `9600`. The baudrate in the code must match the baudrate specified via the motor controller's dipswitches ([see motor controller datasheet for dipswitch configurations](https://github.com/hannabanana96/MPDR_Masters/blob/master/docs/datasheets/smartdriveduo-smart-dual-channel-30a-motor-driver-datasheet.pdf)). If a different serial port is set up in the Hardware setup stage, the provided code will need to be updated accordingly as well. 

Assuming the `mpdr` package and ROS are installed on the Pi, the motor controller code can be tested by the following:
* Start a roscore
* In a new terminal: `rosrun mpdr mtr_ctrl.py`
* `sudo apt install ros-noetic-teleop-twist-keyboard`
* `rosrun teleop_twist_keyboard teleop_twist_keyboard.py`
Now you can control the motors via screen keyboard. This method is recommended for manually controlling the robot when needing to move it from place to place. I also recommend testing the software before connecting the Pi to the motor controller by connecting the Pi's respective UART pins to a COM output screen to check that the messages being sent are as expected. 

Read through mtr_ctrl.py for further information on how the serial messages are created.

