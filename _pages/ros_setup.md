---
permalink: /ros_setup/
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
This indicates to ROS that the MASTER_Pi is the master. \
Launch a ROS score on the master (`roscore`) and make sure the ONBOARD_Pi can see the topics (`rostopic list`)

# Install & Setup of Sensor Packages
## GPS - NEO-M9N
ROS Package: <https://github.com/KumarRobotics/ublox>
`git clone https://github.com/KumarRobotics/ublox.git` \
Communication protocol with Rasp Pi: UART via ttyS0\
Power: 3.3V or 5V

On the Raspberry Pi, UART needs to be enabled:
`sudo vi /boot/firmware/usercfg.txt` \
Add/change `enable_uart=1` \
Reboot to activate

** Write down how to check that this was enabled **

Also might need to set the baud rate for ttyS0 to 38400
> `stty -F /dev/ttyS0 38400`

GPS Wiring Guide:
GPS_5V - pi_5V \
      or \
GPS_3V3 - pi_3V3
GPS_GND - pi_GND
GPS_RX - pi_TX (GPIO_14)
GPS_TX - pi_RX (GPIO_15)

| Type of Stressor | Example | Impact/Outcome |
| :--------------- | ------- | -------------: |
