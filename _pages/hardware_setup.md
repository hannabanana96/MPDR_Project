---
permalink: /hardware_setup/
---
# Setup Raspberry Pi 4 with Ubuntu Mate and ROS Noetic
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
set number        " Turns on row numbers
set tabstop=4     " Tab = 4 spaces
set shiftwidth=4  " Shift = 4 spaces
set expandtab     " Tabs turn into spaces (good for python programming
set autoindent    
set smartindent
colorscheme koehler " I just like this color scheme
syntax on           " Turns on syntax highlighting
```

* [Setup SSH capabilities](https://www.raspberrypi.org/documentation/remote-access/ssh/)
