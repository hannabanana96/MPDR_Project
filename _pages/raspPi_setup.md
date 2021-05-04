---
permalink: /raspPi_setup/
title: Raspberry Pi Setup
tags:
  - alignment
  - captions
  - content
  - css
  - image
  - markup
---
# Setup Raspberry Pi 4 with Ubuntu Mate
As far as I found, Ubuntu Mate 20.04 LTS is the current "best" distribution for running ROS Noetic on a Raspberry Pi 4. \
*LTS: Long Term Support

Download [Ubuntu Mate 20.04](https://ubuntu-mate.org/raspberry-pi/download/) from their website on to your local computer.
arm64 is for 64bit architecture and armhf is for 32bit architecture.For the Raspberry Pi 4, we chose to use the arm64 version.
Unzip the downloaded file and flash to an SD card with at least 8GB (I recommend a 32GB SD card) and using [balenaEtcher](https://www.balena.io/etcher/).
Insert the SD card into the Raspberry Pi, connect a monitor via the mini-HDMI cable and a USB keyboard/mouse. Turn on the Pi!

Go through Ubuntu's setup procedure. Once the setup procedure is complete, leave the Pi running for a few hours as it does system updates in the background via `apt` these can be seen by typing `top` into the command line.

Once the background updates are complete, run `sudo apt-get update` and `sudo apt-get upgrade`.

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

# Install [ROS Noetic](http://wiki.ros.org/noetic/Installation/Ubuntu) on the Raspberry Pi 4
Follow the instructions in the link. When selecting which installation to chose, I suggest the Desktop Install: `sudo apt install ros-noetic-desktop`
This includes rviz (a data visualization tool) which will come in handy for testing and debugging.

Proceed to [ROS Tutorials](http://wiki.ros.org/ROS/Tutorials) to setup a catkin_ws - the location of where all the ROS packages and source code. Complete the first tutorial: [Installing and Configuring Your ROS Environment](http://wiki.ros.org/ROS/Tutorials/InstallingandConfiguringROSEnvironment). I also recommend spending time with the Beginning Level tutorials located on the [ROS Tutorials](http://wiki.ros.org/ROS/Tutorials) page to become accustom to ROS. It is a steep learning curve, but will eventually make one's life easier.

# Rasp Pi -> Motor Controller (UART)
The Rasp Pi 4 has six UART (RX/TX) ports to choose from ([raspberrypi.org info](https://www.raspberrypi.org/documentation/configuration/uart.md)). For our purposes, I chose UART2 which is not natively activited. We can activate UART2 by doing the following:
`sudo vi /boot/firmware/usercfg.txt` \
And add: `dtoverlay=UART2,<specify baudrate>` \
Then reboot. \
*The baudrate is going to dependent on what you specify in your code and on the motor controller hardware via the dipswitches ([see motor controller datasheet](https://github.com/hannabanana96/MRPD_Masters/blob/main/smartdriveduo-smart-dual-channel-30a-motor-driver-datasheet.pdf))

To make sure the new UART port is open: `python3 -m serial.tools.list_ports`, you should see an additional tty/AMA0 (I think)

# Rasp Pi -> Wheel Encoders (SPI)
Serial Peripheral Interface (SPI) is not natively actived on the Rasp Pi 4, which has up to 6 SPI ports. For our purposes, I chose SPI0. To activate SPI do the following:
`sudo vi /boot/firmware/usercfg.txt` \
And add: `dtparam=spi=on`
Also: `pip3 install spidev`
Then reboot.
Check `ls -l /dev/spidev*`. You should see two lines with SPI.
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/spi_cmdline.JPG){: .align-center}


This automatically addes two chip select lines (by default you can run two devices on this SPI line). Next, check that your user has permissions to access the the SPI ports: `ls -l /dev/spi*` Need to finish this, add a picture


