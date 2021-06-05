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
* In `/boot/firmware/usercfg.txt`, add `dtoverlay=uart1

# Software Setup
