
# Installation & Setup

The installation process for the vision system is likely the most complicated part. So, in an attempt to streamline the process
we have provided enough documentation to either install from a backup file or build from source.

## Installing WiFi Module

Refer to [This Link](https://kb.vex.com/hc/en-us/articles/360048489132-Installing-the-Intel-Dual-Band-Wi-Fi-and-Antennas-for-VEX-AI) that
properly explains how to install the WiFi module onto the Jetson Nano

## Install WhoopLib OS On Jetson

You will require a MicroSD card with at least 64GB of memory. 32GB will not cut it.

#### You have two options to setup the Jetson Nano (select one):

<!-- tabs:start -->

#### **Install from Image**

> 1. Download the compressed .img from here:\
> For Jetson Nano: [File Link](https://drive.google.com/file/d/1dwa_KmGzVp8BOQCDQ5cHc-nW-zZzIjw9/view?usp=sharing)\
> For Jetson Nano Orion: Not Available Yet
> 2. Extract whooplib_vision.zip file:\
> Right click the file -> Extract Here
> 3. Download Balena Etcher: [Download Page](https://github.com/balena-io/etcher/releases/)\
> Windows: Download the x64 .exe and run the installer\
> Linux: Download the .deb, Right Click -> Open With Other Application -> Software Install
> 4. Run Balena Etcher and etch the whooplib_vision.img onto your MicroSD Card\
> This process may take 30-60 minutes.
> 5. Put your MicroSD Card into the port on the Jetson Nano
> 6. Connect a monitor to the Nano and power it\
> It may take a while (5-10 minutes), but you should expect it to display the desktop.

#### **Build From Source**
> For Jetson Nano: [Instructions](https://docs.google.com/document/d/1R466WGGEFfLnCq74Ui_tFQveaQ1RHnSQTE2j4t9e8I4/edit?usp=sharing)\
> For Jetson Nano Orion: Not Available Yet

<!-- tabs:end -->

## Connect Jetson Nano to WiFi

1. Connect a montitor, keyboard, and mouse to the Jetson Nano

2. Connect to the wifi network (Image for Reference):\
![Image](../images/JetsonWifi.png)

3. Click "Connection Information" and take note of the ip address. This is the IP address for your Jetson Nano for the next step\
![Image](../images/LocalIP.png)


## Update to Latest Deployment

1. SSH into your jetson nano via "```ssh jetson@your_jetson_ip```"\
With "your_jetson_ip" as the ip address of the jetson on the network.

2. Password should be "```jetson```"

3. Run the following to update to the latest version of the WhoopLibPython and restart the subsystem:
```bash
  cd ~/Desktop/WhoopLibPython

  git pull

  sudo systemctl restart whooplibpython.service
```

