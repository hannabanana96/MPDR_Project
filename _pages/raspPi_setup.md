---
permalink: /raspPi_setup/
title: Raspberry Pi Setup
---
# Setup Raspberry Pi 4 with Ubuntu Mate
As far as I found, Ubuntu Mate 20.04 LTS is the current "best" distribution for running ROS Noetic on a Raspberry Pi 4. \
*LTS: Long Term Support

Download [Ubuntu Mate 20.04](https://ubuntu-mate.org/raspberry-pi/download/) from their website on to your local computer.
arm64 is for 64bit architecture and armhf is for 32bit architecture. For the Raspberry Pi 4, we chose to use the arm64 version.
Unzip the downloaded file and flash to an SD card with at least 8GB (I recommend a 32GB SD card) using [balenaEtcher](https://www.balena.io/etcher/).
Insert the SD card into the Raspberry Pi, connect a monitor via the mini-HDMI cable and a USB keyboard/mouse. Turn on the Pi!

Go through Ubuntu's setup procedure. Once the setup procedure is complete, leave the Pi running for a few hours as it does system updates in the background via `apt` these can be seen by typing `top` into the command line.

Once the background updates are complete, run `sudo apt update` and `sudo apt upgrade`.

Other things I suggest installing before moving forward with ROS:
* `sudo apt install net-tools`: This gets you ifconfig networking tools so you can see the Pi's IP address
* Setup VIM or your favorite text editor
`sudo apt install vim`
Here are my recommended vim settings:
```
set number          " Turns on row numbers
set tabstop=4       " Tab = 4 spaces
set shiftwidth=4    " Shift = 4 spaces
set expandtab       " Tabs turn into spaces (good for python programming
set autoindent    
set smartindent
colorscheme koehler " I just like this color scheme
syntax on           " Turns on syntax highlighting
```

* [Setup SSH capabilities](https://www.raspberrypi.org/documentation/remote-access/ssh/) - I recommend the systemctl method (Ubuntu Mate 20.04 doesn't have raspi-config). Setting up ssh will allow you to access the Rasp Pi's file system and command line from another machine.
* `sudo apt install x11-apps`: This gets you `xeyes`. Typing `xeyes` into the command line allows a pair of eyes to pop on the graphical interface so you can check whether the Pi is displaying the correct screen/interface. (Good for checking if you set up your SSH X11 forwarding correctly).
* `sudo apt install tmux`: Tmux allows you to have multiple shells open in the window terminal window - very helpful when developing in ROS. ROS requires a lot of different scripts running simultaneously.

# Install [ROS Noetic](http://wiki.ros.org/noetic/Installation/Ubuntu) on the Raspberry Pi 4
Follow the instructions in the link. When selecting which installation to chose, I suggest the Desktop Install: `sudo apt install ros-noetic-desktop`
This includes rviz (a data visualization tool) which will come in handy for testing and debugging.

Proceed to [ROS Tutorials](http://wiki.ros.org/ROS/Tutorials) to setup a catkin_ws - the location of where all the ROS packages and source code. Complete the first tutorial: [Installing and Configuring Your ROS Environment](http://wiki.ros.org/ROS/Tutorials/InstallingandConfiguringROSEnvironment). I also recommend spending time with the Beginning Level tutorials located on the [ROS Tutorials](http://wiki.ros.org/ROS/Tutorials) page to become accustom to ROS. It is a steep learning curve, but will eventually make life easier.

Some key ROS commands that will be helpful for setting up and debugging the senors nodes
* `rostopic list`: lists all the ROS topics that have been initiated by the nodes
* `rostopic echo <topic_name>`: prints the topic's messages
* `rostopic echo -c <topic_name>`: clears the message before echo'ing the next message
* `rosnode list`: lists all the ROS nodes that have been initiated

# Communicating with Multiple Devices Running ROS
First, the two devices must be on the same network in order to communicate with each other. To check if the devices can properly communicate with each other, follow the [ROS tutorial on Full Connectivity](http://wiki.ros.org/ROS/NetworkSetup), checking both `ping` and `netcat`.

We'll call the two Pi's: robot_pi, and other_pi.
In the ~/.bashrc file of the robot_pi, add the following:
```
export ROS_IP=<public IP of the robot_pi>
export ROS_MASTER_URI=http://<public IP of robot_pi>:11311
```
In the ~/.bashrc file of the other_pi, add the following:
```
export ROS_IP=<public IP of the other_pi>
export ROS_MASTER_URI=http://<public IP of robot_pi>:11311
```
This indicates to ROS that the robot_pi is the master. \
Launch a ROS score on the master (`roscore`) and make sure the other_pi can see the topics (`rostopic list`)



# Sensor and Motor Setups
* [Lidar](https://hannabanana96.github.io/MPDR_Project/lidar/)
* [GPS](https://hannabanana96.github.io/MPDR_Project/gps/)
* [IMU](https://hannabanana96.github.io/MPDR_Project/imu/)
* [Motor Controller](https://hannabanana96.github.io/MPDR_Project/motor_controller/)
* [Wheel Encoder](https://hannabanana96.github.io/MPDR_Project/wheelEncoder/)


# Permission denied for GPIO/I2C/SPI access?
This means that these busses currently require "sudo" access to run. Here is how to fix this by making a change to the udev-system to change ownership of the SPI/I2C/GPIO busses.

1. Check to see if you have access to the port
`ls -l /dev/spi*`
![image-center](https://hannabanana96.github.io/MPDR_Project/assets/images/spi_cmdline.JPG){: .align-center}
If the fourth column says "root" (unlike the picture), you the user do not have access to the port and should continue to follow these steps. 
2. Look to see if there is already a "spi" group present on the Pi: `$ groups`. If there is not, create one: `sudo groupadd spi`
3. Add your user to the group: `sudo adduser "$USER" spi`
4. Create a rules file: `sudo vi /etc/udev/rules.d/50-spi.rules` with the following contents:
`KERNEL=="spidev*", GROUP="spi", MODE="0660"`
5. Reboot and `ls -l /dev/spi*` to check that the "spi" group now owns the activated SPI busses

