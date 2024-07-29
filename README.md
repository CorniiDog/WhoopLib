![WhoopLib Logo](/docs/images/WhoopLibWhite.png)

<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->

The most advanced SLAM solution in VEX.

**With both PROS and VEXCode support**

#### **Heads up:** Slew rate is added. That does mean I would need to tune the default pure pursuit parameters to work for most robot types.

![GitHub Release](https://img.shields.io/github/v/release/ConnorAtmos/WhoopLib?style=for-the-badge)
![GitHub forks](https://img.shields.io/github/forks/ConnorAtmos/WhoopLib?style=for-the-badge&color=%23500)
![GitHub Repo stars](https://img.shields.io/github/stars/ConnorAtmos/WhoopLib?style=for-the-badge&color=%23500)
![Github Issues](!https://img.shields.io/github/issues/ConnorAtmos/WhoopLib?style=for-the-badge)
[![LinkedIn][linkedin-shield]][linkedin-url]
<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/connor-white-38a5501a0/


## Getting Started

To start off with WhoopLib, head to: https://connoratmos.github.io/WhoopLib/

## Links

[WhoopLib Documentation](https://connoratmos.github.io/WhoopLib/)

[WhoopLib Zero - Learn C++ From Zero](https://connoratmos.github.io/WhoopLibZero/)

[WhoopLibVEXCode Github](https://github.com/ConnorAtmos/WhoopLibVEXCode)

[WhoopLibPROS Github](https://github.com/ConnorAtmos/WhoopLibPROS)

[WhoopLibPython Github](https://github.com/ConnorAtmos/WhoopLibPython)

[Release Notes](https://github.com/ConnorAtmos/WhoopLib/releases/)

## Features

- Visual Odometry/Pose Estimation
- Wheel Odometry/Pose Estimation, inspired by JAR-Template
- Communication between V5 Brain and Jetson Nano
- Fusion Odometry between Visual Odometry and Wheel Odometry
- Rolling Average Filter for Fusion Odometry
- WhoopController Class with auto-configuration for Split Arcade, Tank, Left Stick Arcade, and Right Stick Arcade
- Dubins-Curves Path Creation, thanks to Andrew Walker
- Pure Pursuit Algorithm
- Moving between Point A and Point B
- General PID, modified from JAR-Template
- Turning by degrees, Turning to degrees, Turning to Face x and y Coordinates
- Moving Forward and Reverse Functions, alongside Remembering Previous Movement Positions for Improved Resilience
- Generated Paths with Waypoints to Navigate around the VEX Robotics Field in One Fell Swoop
- Well-Rounded and Continuously Updated Documentation for a Low Floor yet High Ceiling
- Slew for Motor Movements
- Motor Voltage to Speed Linearization
- TwoDPose Class which Handles and Simplifies Linear Algebra Mathematics, Following Similarities to Roblox's CFrames for Ease of Use

## Roadmap

- Object Detection and Gridded Permanence system
- Detecting other robots that impede the path of the robot, and drive around
- Implementation of a Jetson Orion Nano instead of the End-Of-Line (EOL) Jetson Nano
- Implementation of a better SLAM solution instead of relying on the EOL Realsense T265 Camera
- Capability to Use Different Devices like Oak-D, etc. for Visual Odometry
- Capability to Use Lidar
- Virtual Highway system

## Acknowledgements

 - [E-Bots πLons](http://thepilons.ca/wp-content/uploads/2018/10/Tracking.pdf): Odometry Documentation
 - [JAR-Template](https://github.com/JacksonAreaRobotics/JAR-Template): Odometry Inspiration
 - [Librealsense](https://github.com/IntelRealSense/librealsense): Depth Capturing
 - [VEX Robotics](https://github.com/VEX-Robotics-AI)
 - [Andrew Walker](https://github.com/AndrewWalker/Dubins-Curves/tree/master): Path Generation with Dubins-Curves
  - [LearnCpp](https://www.learncpp.com/): Resource for learning C++

<!-- LICENSE -->
## License

Distributed under the [MIT](https://choosealicense.com/licenses/mit/) License.

<!-- CONTACT -->
## Contact

Connor White - connor.sw.personal@gmail.com

