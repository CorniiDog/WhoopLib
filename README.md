![WhoopLib Logo](/docs/images/WhoopLibWhite.png)

<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->

![GitHub Release](https://img.shields.io/github/v/release/CorniiDog/WhoopLib?style=for-the-badge
![GitHub forks](https://img.shields.io/github/forks/CorniiDog/WhoopLib?style=for-the-badge
![GitHub Repo stars](https://img.shields.io/github/stars/CorniiDog/WhoopLib?style=for-the-badge)


A Simple SLAM Solution.

**With both PROS and VEXCode support**

## Getting Started

To start off with WhoopLib, head to: https://CorniiDog.github.io/WhoopLib/

## Links

[WhoopLib Documentation](https://CorniiDog.github.io/WhoopLib/)

[WhoopLibVEXCode Github](https://github.com/CorniiDog/WhoopLibVEXCode)

[WhoopLibPROS Github](https://github.com/CorniiDog/WhoopLibPROS)

[WhoopLibPython Github](https://github.com/CorniiDog/WhoopLibPython)

## Features

### Odometry & Pose Estimation
- Visual odometry / pose estimation
- Wheel odometry / pose estimation (inspired by JAR-Template)
- Fusion odometry (visual + wheel) with rolling average filter

### Control & Motion
- `WhoopController` with auto-configuration for Split Arcade, Tank, Left Stick Arcade, and Right Stick Arcade
- Path generation: Dubins curves (thanks to Andrew Walker) and Pure Pursuit
- Point-to-point navigation (move between Point A and Point B)
- High-level motion primitives: precise turning (by degrees / to face coordinates), forward/reverse movement with historical position memory for resilience
- General PID controller with anti-windup (`kR`, aka "retracted windup")
- Slew rate limiter for motor movements
- Motor voltage-to-speed linearization

### Utilities & Abstractions
- `TwoDPose` class simplifying linear algebra, modeled with similarities to Roblox’s CFrames
- Units system (`_in`, `_v`, `_mm`, etc.)
- Simplified MicroSD card file system
- Autonomous selector with optional persistent saving via MicroSD

### Documentation & Ecosystem
- Comprehensive and continuously updated documentation for a low floor and high ceiling

## Roadmap (Needs Maintainer)

- Object Detection and Gridded Permanence system
- Detecting other robots that impede the path of the robot, and drive around
- Implementation of a Jetson Orion Nano instead of the End-Of-Line (EOL) Jetson Nano
- Implementation of a better SLAM solution instead of relying on the EOL Realsense T265 Camera
- Capability to Use Different Devices like Oak-D, etc. for Visual Odometry
- Capability to Use Lidar
- Virtual Highway system

>[!IMPORTANT]
>WhoopLib is an independent, student-led project and is not endorsed by or affiliated with Texas A&M University.

>[!IMPORTANT]
>The Python/Jetson backend is experimental. There is no automatic ML data transfer to V5; users must implement their own integration. Odometry fusion depends on the factory calibration quality of the T265, which can vary over time and between units.

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

