# Configuring Odometry Fusion

This will probably be one of the simplest tutorials out there. The odometry fusion is a module that manages the vision odometry and the wheel odometry to fuse them together as one odometry unit. It also acts as a singleton for just wheel odometry only if necessary 

<!-- tabs:start -->

#### **If You Have a Vision Tesseract**

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
WhoopOdomFusion odom_fusion(
    &vision_system,              // Pointer to the vision system
    &odom_offset,                // Pointer to the odometry offset
    0.9,                         // Minimum confidence threshold to apply vision system to odometry
    fusionmode::fusion_gradual,  // The method of fusing
    50_in,               // If fusionmode is fusion_gradual, it is the maximum allowable lateral shift the vision camera can update.
    500_deg                  // If fusionmode is fusion_gradual, it is the maximum allowable yaw rotational shift the vision camera can update.
);
```

<!-- tabs:end -->

This odometry fusion object has some unique features. First of all the ```0.9``` you see is the minimum confidence threshold to accept the T265 pose into the fusion object. If the T265 is not as confident about its tracking, the odometry fusion would reject the pose below the given threshold. This confidence number is a range between ```0.0``` and ```1.0```, where around ```0.0``` implies accepting even failing poses, ```0.3``` accepts all at around low confidence, ```0.6``` at respectable confidence, and ```1.0``` at really high (near-impossible) confidence.

There are multiple fusion modes:

| ```fusionmode```     | Definition | 
|----------|:--------:|
| ```fusion_gradual```    | Gradually fuses the vision odometry with the wheel odometry     |
| ```fusion_instant```    | Immediately replaces the vision odometry when a pose is retreived     |
| ```vision_only```    | Only accept odometry from the vision system, ignoring wheel odometry     |
| ```wheel_odom_only```    | Only accepts wheel odometry, ignoring vision odometry     |

If ```fusionmode``` is set to ```fusion_gradual``` you can adjust the maximum amount of xy and yaw shift per second, hence the last two variables ```50_in``` and ```500_deg```.


#### **If You Do Not Have a Vision Tesseract**

This is super difficult, so take a breather before starting.

Are you ready?

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
WhoopOdomFusion odom_fusion(&odom_offset);
```

<!-- tabs:end -->


Now you are ready for the next step: [Creating Drivetrain Object](CreatingDrivetrainObject/README.md)

<!-- tabs:end -->