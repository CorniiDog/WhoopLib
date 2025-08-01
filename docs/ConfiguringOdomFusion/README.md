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
    50_in,                       // If fusionmode is fusion_gradual, it is the maximum allowable lateral shift the vision camera can update.
    500_deg                      // If fusionmode is fusion_gradual, it is the maximum allowable yaw rotational shift the vision camera can update.
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

<!-- tabs:start -->

#### **Examples**

Look at the other tabs to view full applied examples of the vision system applied.

#### **Applied: PROS**

```cpp
/**
 * Module:       main.cpp
 * Author:       Connor White   
 * Created:      Thu Jun 21 2024
 * Description:  Whooplib Template
 *
 * Contributions:
 *   2775 Josh:
 *      https://github.com/JacksonAreaRobotics/JAR-Template
 *   Intel:
 *      https://github.com/IntelRealSense/librealsense
 *   PiLons:
 *      http://thepilons.ca/wp-content/uploads/2018/10/Tracking.pdf
 *   Andrew Walker:
 *      https://github.com/AndrewWalker/Dubins-Curves/tree/master
 *   Alex:
 *      https://www.learncpp.com/
 *
 */
#include "main.h"

////////////////////////////////////////////////////////////
/**
 *    Globals
 */
////////////////////////////////////////////////////////////

// Primary controller
WhoopController controller1(joystickmode::joystickmode_split_arcade, controllertype::controller_primary);

// Left drive motors
WhoopMotor l1(PORT12, cartridge::blue, reversed::yes_reverse);
WhoopMotor l2(PORT13, cartridge::blue, reversed::yes_reverse);
WhoopMotor l3(PORT14, cartridge::blue, reversed::yes_reverse);
WhoopMotor l4(PORT15, cartridge::blue, reversed::yes_reverse);
WhoopMotorGroup left_motors({&l1, &l2, &l3, &l4});

// Right drive motors
WhoopMotor r1(PORT1, cartridge::blue, reversed::no_reverse);
WhoopMotor r2(PORT2, cartridge::blue, reversed::no_reverse);
WhoopMotor r3(PORT3, cartridge::blue, reversed::no_reverse);
WhoopMotor r4(PORT4, cartridge::blue, reversed::no_reverse);
WhoopMotorGroup right_motors({&r1, &r2, &r3, &r4});

// Sensors
WhoopInertial inertial_sensor(PORT7);
WhoopRotation forward_tracker(PORT6, reversed::no_reverse);
WhoopRotation sideways_tracker(PORT9, reversed::no_reverse);

// ////////////////////////////////////////////////////////////
// /**
//  *    Wheel Odometry Configuration
//  */
// ////////////////////////////////////////////////////////////

WhoopDriveOdomUnit odom_unit(
    1.51_in,          // The forward tracker distance from the odom unit's center. (positive implies a shift to the right from the odom unit's center)
    2.5189_in,        // Diameter of the forward tracker (e.g., 3.25_in for 3.25-inch wheels).
    -4.468_in,        // The sideways tracker distance from the odom unit's center (positive implies a shift forward from the odom unit center)
    2.5189_in,        // Diameter of the sideways tracker (e.g., 3.25_in for 3.25-inch wheels).
    &inertial_sensor, // Pointer to the WhoopInertial sensor
    &forward_tracker, // Pointer to the forward tracker, as a WhoopRotation sensor
    &sideways_tracker // Pointer to the sideways tracker, as a WhoopRotation sensor
);

WhoopDriveOdomOffset odom_offset(
    &odom_unit, // Pointer to the odometry unit (will manage the odom unit)
    -0.6_in,    // The x offset of the odom unit from the center of the robot (positive implies a shift right from the center of the robot).
    4.95_in     // The y offset of the odom unit from the center of the robot (positive implies a shift forward from the center of the robot).
);

////////////////////////////////////////////////////////////
/**
 *    Wheel Odometry Fusion (No Vision System)
 */
////////////////////////////////////////////////////////////
WhoopOdomFusion odom_fusion(
    &odom_offset
);

////////////////////////////////////////////////////////////
/**
 *    Pure Pursuit Default Parameters
 */
////////////////////////////////////////////////////////////

PursuitParams pursuit_parameters(
    /////////////////////////
    // Path Generation
    /////////////////////////
    // Radius of the turns
    5_in 

    /////////////////////////
    // Pure Pursuit
    /////////////////////////
    // Pure Pursuit look ahead distance
    ,5_in
    // The number of points when generating the path. More points mean higher detail of the path, but at a higher computational cost
    ,100_points 

    /////////////////////////
    // Motor Voltages
    /////////////////////////
    // Pure pursuit forward max motor voltage (0.0, 12.0]
    ,8.0_volts
    // Pure pursuit turning max motor voltage (0.0, 12.0]
    ,12.0_volts

    /////////////////////////
    // Settling
    /////////////////////////
    // Settle Distance. Exits when within this distance of target
    ,1.25_in
    // Settle Rotation. Exits when within this rotation of target
    ,1.1_deg
    // Minimum time to be considered settled, in seconds
    ,0.0_sec
    // Time after which to give up and move on, in seconds (set to 0 to disable)
    ,0_sec
    
    /////////////////////////
    // Turning PID
    /////////////////////////
    // Turning (kP) Proportional Tuning
    ,14_kp
    // Turning (kI) Integral Tuning
    ,0.2_ki
    // Turning (kD) Derivative Tuning
    ,95.0_kd
    // Turning (kR) Integral anti-windup Tuning. Higher value implies greater anti-windup near error=0. 
    // NOTE: Affected by turning_i_activation
    ,1.0_kr
    // The rotation distance (error) to activate turning_ki
    ,20.0_deg
    // The maximum turning voltage change per second, as a slew rate
    ,250.0_volts

    /////////////////////////
    // Forward PID
    /////////////////////////
    // Forward (kP) Proportional Tuning
    ,50.0_kp
    // Forward (kI) Integral Tuning
    ,0.1_ki
    // Forward (kD) Derivative Tuning
    ,250.0_kd
    // Forward (kR) Integral anti-windup Tuning. Higher value implies greater anti-windup near error=0. 
    // NOTE: Affected by forward_i_activation
    ,0.0_kr
    // The forward distance (error) to activate forward_ki
    ,2.0_in
    // The maximum forward voltage change per second, as a slew rate
    ,150.0_volts
);

////////////////////////////////////////////////////////////
/**
 *    Robot Drivetrain and Manager
 */
////////////////////////////////////////////////////////////
WhoopDrivetrain robot_drivetrain(
    &pursuit_parameters,  // The default pure pursuit parameters for operating the robot in autonomous
    &odom_fusion,         // Odometry fusion module
    PoseUnits::in_deg_cw, // Set default pose units if not defined. "m_deg_cw" means "meters, degrees, clockwise-positive yaw", "in_deg_ccw" means "inches, degrees, counter-clockwise-positive yaw", and so forth.
    &controller1,         // Pointer to the controller
    &left_motors,         // Pointer to the left motor group (optionally can be a list of motors as well)
    &right_motors         // Pointer to the right motor group (optionally can be a list of motors as well)
);

/**
 * My first autonomous routine
 */
void auton_1(){
    robot_drivetrain.set_pose_units(PoseUnits::in_deg_cw);
    robot_drivetrain.set_pose(0, 0, 0);

    // robot_drivetrain.turn_to_position(15, 15);
    robot_drivetrain.drive_forward(15);

    robot_drivetrain.turn_to(90);

    robot_drivetrain.drive_forward(-15);

    robot_drivetrain.drive_forward(15);

    robot_drivetrain.turn_to(0);

    robot_drivetrain.drive_forward(-15);

    // robot_drivetrain.drive_to_point(15, 15);
    // robot_drivetrain.reverse_to_point(0,0);
    robot_drivetrain.drive_through_path({{15, 15, 0}, {0, 0, 90}}, 7);
    robot_drivetrain.reverse_through_path({{15, 15, 180}, {0, 0, 180}}, 7);
}

/**
 * My second autonomous routine
 */
void auton_2(){

}

/**
 * My third autonomous routine
 */
void auton_3(){

}

WhoopAutonSelector auton_selector(&controller1, {
    AutonRoutine("First Auton", auton_1),
    AutonRoutine("Second Auton", auton_2),
    AutonRoutine("Third Auton", auton_3)
}, "auton.txt");

ComputeManager manager({&robot_drivetrain, &controller1, &auton_selector});

/**
 * Runs initialization code. This occurs as soon as the program is started.
 *
 * All other competition modes are blocked by initialize; it is recommended
 * to keep execution time for this mode under a few seconds.
 */
void initialize()
{
    auton_selector.run_selector();
    controller1.notify("Initializing");
    manager.start();
}

/**
 * Runs while the robot is in the disabled state of Field Management System or
 * the VEX Competition Switch, following either autonomous or opcontrol. When
 * the robot is enabled, this task will exit.
 */
void disabled()
{
    robot_drivetrain.set_state(drivetrainState::mode_disabled);
}

/**
 * Runs after initialize(), and before autonomous when connected to the Field
 * Management System or the VEX Competition Switch. This is intended for
 * competition-specific initialization routines, such as an autonomous selector
 * on the LCD.
 *
 * This task will exit when the robot is enabled and autonomous or opcontrol
 * starts.
 */
void competition_initialize() {}

/**
 * Runs the user autonomous code. This function will be started in its own task
 * with the default priority and stack size whenever the robot is enabled via
 * the Field Management System or the VEX Competition Switch in the autonomous
 * mode. Alternatively, this function may be called in initialize or opcontrol
 * for non-competition testing purposes.
 *
 * If the robot is disabled or communications is lost, the autonomous task
 * will be stopped. Re-enabling the robot will restart the task, not re-start it
 * from where it left off.
 */
void autonomous()
{
    robot_drivetrain.set_state(drivetrainState::mode_autonomous);
    auton_selector.run_autonomous();
}

/**
 * Runs the operator control code. This function will be started in its own task
 * with the default priority and stack size whenever the robot is enabled via
 * the Field Management System or the VEX Competition Switch in the operator
 * control mode.
 *
 * If no competition control is connected, this function will run immediately
 * following initialize().
 *
 * If the robot is disabled or communications is lost, the
 * operator control task will be stopped. Re-enabling the robot will restart the
 * task, not resume it from where it left off.
 */
void opcontrol()
{
    robot_drivetrain.set_state(drivetrainState::mode_usercontrol);
    
    while (true)
    {
        pros::delay(20); // Run for 20 ms then update
    }
}

```

#### **Applied: VEXCode**

```cpp
/*----------------------------------------------------------------------------*/
/*                                                                            */
/*    Module:       main.cpp                                                  */
/*    Author:       Connor White                                              */
/*    Created:      Thu Jun 21 2024                                           */
/*    Description:  Whooplib Template                                         */
/*                                                                            */
/*    Contributions:                                                          */
/*      2775 Josh:                                                            */
/*        https://github.com/JacksonAreaRobotics/JAR-Template                 */
/*      Intel:                                                                */
/*        https://github.com/IntelRealSense/librealsense                      */
/*      PiLons:                                                               */
/*        http://thepilons.ca/wp-content/uploads/2018/10/Tracking.pdf         */
/*      Andrew Walker:                                                        */
/*        https://github.com/AndrewWalker/Dubins-Curves/tree/master           */
/*      Alex:                                                                 */
/*        https://www.learncpp.com/                                           */
/*                                                                            */
/*----------------------------------------------------------------------------*/
#include "vex.h"
#include "whooplib.h"

using namespace vex;

// A global instance of competition
competition Competition;

////////////////////////////////////////////////////////////
/**
 *    Globals
 */
////////////////////////////////////////////////////////////

// Primary controller
WhoopController controller1(joystickmode::joystickmode_split_arcade, controllertype::controller_primary);

// Left drive motors
WhoopMotor l1(PORT12, cartridge::blue, reversed::yes_reverse);
WhoopMotor l2(PORT13, cartridge::blue, reversed::yes_reverse);
WhoopMotor l3(PORT14, cartridge::blue, reversed::yes_reverse);
WhoopMotor l4(PORT15, cartridge::blue, reversed::yes_reverse);
WhoopMotorGroup left_motors({&l1, &l2, &l3, &l4});

// Right drive motors
WhoopMotor r1(PORT1, cartridge::blue, reversed::no_reverse);
WhoopMotor r2(PORT2, cartridge::blue, reversed::no_reverse);
WhoopMotor r3(PORT3, cartridge::blue, reversed::no_reverse);
WhoopMotor r4(PORT4, cartridge::blue, reversed::no_reverse);
WhoopMotorGroup right_motors({&r1, &r2, &r3, &r4});

// Sensors
WhoopInertial inertial_sensor(PORT7);
WhoopRotation forward_tracker(PORT6, reversed::no_reverse);
WhoopRotation sideways_tracker(PORT9, reversed::no_reverse);

// ////////////////////////////////////////////////////////////
// /**
//  *    Wheel Odometry Configuration
//  */
// ////////////////////////////////////////////////////////////

WhoopDriveOdomUnit odom_unit(
    1.51_in,          // The forward tracker distance from the odom unit's center. (positive implies a shift to the right from the odom unit's center)
    2.5189_in,        // Diameter of the forward tracker (e.g., 3.25_in for 3.25-inch wheels).
    -4.468_in,        // The sideways tracker distance from the odom unit's center (positive implies a shift forward from the odom unit center)
    2.5189_in,        // Diameter of the sideways tracker (e.g., 3.25_in for 3.25-inch wheels).
    &inertial_sensor, // Pointer to the WhoopInertial sensor
    &forward_tracker, // Pointer to the forward tracker, as a WhoopRotation sensor
    &sideways_tracker // Pointer to the sideways tracker, as a WhoopRotation sensor
);

WhoopDriveOdomOffset odom_offset(
    &odom_unit, // Pointer to the odometry unit (will manage the odom unit)
    -0.6_in,    // The x offset of the odom unit from the center of the robot (positive implies a shift right from the center of the robot).
    4.95_in     // The y offset of the odom unit from the center of the robot (positive implies a shift forward from the center of the robot).
);

////////////////////////////////////////////////////////////
/**
 *    Wheel Odometry Fusion (No Vision System)
 */
////////////////////////////////////////////////////////////
WhoopOdomFusion odom_fusion(
    &odom_offset
);

////////////////////////////////////////////////////////////
/**
 *    Pure Pursuit Default Parameters
 */
////////////////////////////////////////////////////////////

PursuitParams pursuit_parameters(
    /////////////////////////
    // Path Generation
    /////////////////////////
    // Radius of the turns
    5_in 

    /////////////////////////
    // Pure Pursuit
    /////////////////////////
    // Pure Pursuit look ahead distance
    ,5_in
    // The number of points when generating the path. More points mean higher detail of the path, but at a higher computational cost
    ,100_points 

    /////////////////////////
    // Motor Voltages
    /////////////////////////
    // Pure pursuit forward max motor voltage (0.0, 12.0]
    ,8.0_volts
    // Pure pursuit turning max motor voltage (0.0, 12.0]
    ,12.0_volts

    /////////////////////////
    // Settling
    /////////////////////////
    // Settle Distance. Exits when within this distance of target
    ,1.25_in
    // Settle Rotation. Exits when within this rotation of target
    ,1.1_deg
    // Minimum time to be considered settled, in seconds
    ,0.0_sec
    // Time after which to give up and move on, in seconds (set to 0 to disable)
    ,0_sec
    
    /////////////////////////
    // Turning PID
    /////////////////////////
    // Turning (kP) Proportional Tuning
    ,14_kp
    // Turning (kI) Integral Tuning
    ,0.2_ki
    // Turning (kD) Derivative Tuning
    ,95.0_kd
    // Turning (kR) Integral anti-windup Tuning. Higher value implies greater anti-windup near error=0. 
    // NOTE: Affected by turning_i_activation
    ,1.0_kr
    // The rotation distance (error) to activate turning_ki
    ,20.0_deg
    // The maximum turning voltage change per second, as a slew rate
    ,250.0_volts

    /////////////////////////
    // Forward PID
    /////////////////////////
    // Forward (kP) Proportional Tuning
    ,50.0_kp
    // Forward (kI) Integral Tuning
    ,0.1_ki
    // Forward (kD) Derivative Tuning
    ,250.0_kd
    // Forward (kR) Integral anti-windup Tuning. Higher value implies greater anti-windup near error=0. 
    // NOTE: Affected by forward_i_activation
    ,0.0_kr
    // The forward distance (error) to activate forward_ki
    ,2.0_in
    // The maximum forward voltage change per second, as a slew rate
    ,150.0_volts
);

////////////////////////////////////////////////////////////
/**
 *    Robot Drivetrain and Manager
 */
////////////////////////////////////////////////////////////
WhoopDrivetrain robot_drivetrain(
    &pursuit_parameters,  // The default pure pursuit parameters for operating the robot in autonomous
    &odom_fusion,         // Odometry fusion module
    PoseUnits::in_deg_cw, // Set default pose units if not defined. "m_deg_cw" means "meters, degrees, clockwise-positive yaw", "in_deg_ccw" means "inches, degrees, counter-clockwise-positive yaw", and so forth.
    &controller1,         // Pointer to the controller
    &left_motors,         // Pointer to the left motor group (optionally can be a list of motors as well)
    &right_motors         // Pointer to the right motor group (optionally can be a list of motors as well)
);

/**
 * My first autonomous routine
 */
void auton_1(){
    robot_drivetrain.set_pose_units(PoseUnits::in_deg_cw);
    robot_drivetrain.set_pose(0, 0, 0);

    // robot_drivetrain.turn_to_position(15, 15);
    robot_drivetrain.drive_forward(15);

    robot_drivetrain.turn_to(90);

    robot_drivetrain.drive_forward(-15);

    robot_drivetrain.drive_forward(15);

    robot_drivetrain.turn_to(0);

    robot_drivetrain.drive_forward(-15);

    // robot_drivetrain.drive_to_point(15, 15);
    // robot_drivetrain.reverse_to_point(0,0);
    robot_drivetrain.drive_through_path({{15, 15, 0}, {0, 0, 90}}, 7);
    robot_drivetrain.reverse_through_path({{15, 15, 180}, {0, 0, 180}}, 7);
}

/**
 * My second autonomous routine
 */
void auton_2(){

}

/**
 * My third autonomous routine
 */
void auton_3(){

}

WhoopAutonSelector auton_selector(&controller1, {
    AutonRoutine("First Auton", auton_1),
    AutonRoutine("Second Auton", auton_2),
    AutonRoutine("Third Auton", auton_3)
}, "auton.txt");

ComputeManager manager({&robot_drivetrain, &controller1, &auton_selector});

/*---------------------------------------------------------------------------*/
/*                          Pre-Autonomous Functions                         */
/*                                                                           */
/*  You may want to perform some actions before the competition starts.      */
/*  Do them in the following function.  You must return from this function   */
/*  or the autonomous and usercontrol tasks will not be started.  This       */
/*  function is only called once after the V5 has been powered on and        */
/*  not every time that the robot is disabled.                               */
/*---------------------------------------------------------------------------*/
void pre_auton()
{
    robot_drivetrain.set_state(drivetrainState::mode_disabled);
    auton_selector.run_selector();

    // Initializing Robot Configuration. DO NOT REMOVE!
    vexcodeInit();
    controller1.notify("Initializing");
    manager.start();
}

/*---------------------------------------------------------------------------*/
/*                                                                           */
/*                              Autonomous Task                              */
/*                                                                           */
/*  This task is used to control your robot during the autonomous phase of   */
/*  a VEX Competition.                                                       */
/*                                                                           */
/*  You must modify the code to add your own robot specific commands here.   */
/*---------------------------------------------------------------------------*/

void autonomous()
{
    robot_drivetrain.set_state(drivetrainState::mode_autonomous);
    auton_selector.run_autonomous();
}

/*---------------------------------------------------------------------------*/
/*                                                                           */
/*                              User Control Task                            */
/*                                                                           */
/*  This task is used to control your robot during the user control phase of */
/*  a VEX Competition.                                                       */
/*                                                                           */
/*  You must modify the code to add your own robot specific commands here.   */
/*---------------------------------------------------------------------------*/
void usercontrol()
{
  robot_drivetrain.set_state(drivetrainState::mode_usercontrol);

  // User control code here, inside the loop
  while (true)
  {
    wait(20, msec);
  }
}

//
// Main will set up the competition functions and callbacks.
//
int main()
{
  Competition.autonomous(autonomous);
  Competition.drivercontrol(usercontrol);
  pre_auton();
  while (true)
    wait(100, msec);
}
```

<!-- tabs:end -->

<!-- tabs:end -->

Now you are ready for the next step: [Creating Drivetrain Object](CreatingDrivetrainObject/README.md)

<!-- tabs:end -->