# Creating Manager Object

So, given that we have objects, everything needs to be managed right? So that is what the manager object does.

Certain objects are nodes. That is that they are inherited from the ```ComputeNode``` class and can have a ```__step()``` method override. If this sounds a bit confusing, let me make it simpler to understand. Some objects need to be controlled and have their own sub-process in the background behind the scenes to function correctly. And that is why some objects are configured to be capable of ran by the manager to provide functions their own independance but yet still have complete control over an object's functionality.

<!-- tabs:start -->

#### **If You Have a Vision Tesseract**

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
ComputeManager manager({&buffer_system, &jetson_commander, &robot_drivetrain, &controller1, $auton_selector});
```

<!-- tabs:end -->

#### **If You Do Not Have a Vision Tesseract**

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
ComputeManager manager({&robot_drivetrain, &controller1, &auton_selector});
```

<!-- tabs:end -->

<!-- tabs:end -->


Pretty straightforward, right?

Then, in your main.cpp add the following to your ```pre_auton``` function for VEXCode, or `initialize` and `disabled` in PROS:

<!-- tabs:start -->

#### **VEXCode**

```cpp
void pre_auton() {
    robot_drivetrain.set_state(drivetrainState::mode_disabled);
    auton_selector.run_selector();

    // Initializing Robot Configuration. DO NOT REMOVE!
    vexcodeInit();
    controller1.notify("Initializing");
    manager.start();
}
```

#### **PROS**

```cpp
void initialize() {
	pros::lcd::initialize();
	pros::lcd::set_text(1, "Hello PROS User!");
	auton_selector.run_selector();
  controller1.notify("Initializing");
  manager.start();
}
```

```cpp
void disabled() {
	robot_drivetrain.set_state(drivetrainState::mode_disabled);
}
```

<!-- tabs:end -->

Add the following to your ```autonomous``` function:

<!-- tabs:start -->

#### **VEXCode**

```cpp
void autonomous() {
  robot_drivetrain.set_state(drivetrainState::mode_autonomous);
  auton_selector.run_autonomous();
}
```

#### **PROS**
```cpp
void autonomous() {
  robot_drivetrain.set_state(drivetrainState::mode_autonomous);
  // Auton code here
}
```

<!-- tabs:end -->


Add the following to your ```usercontrol``` function in VEXCode, or `opcontrol` function in PROS:

<!-- tabs:start -->

#### **VEXCode**

```cpp
void usercontrol() {
  robot_drivetrain.set_state(drivetrainState::mode_usercontrol);

  while (1) {
    wait(100, msec); // Keep-alive and prevent wasted resources
  }
}
```

#### **PROS**

```cpp
void opcontrol() {
	robot_drivetrain.set_state(drivetrainState::mode_usercontrol);

	while (true) {
		pros::delay(20);                               // Run for 20 ms then update
	}
}
```


<!-- tabs:end -->

So, just a run-down. Your main.cpp may contain something like:

<!-- tabs:start -->

#### **VEXCode**

#### `main.cpp`

```cpp
// Code and additional configuration above...

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
    robot_drivetrain.set_pose_units(PoseUnits::in_deg_cw);
    robot_drivetrain.set_pose(-30, -10, 0);

    robot_drivetrain.drive_forward(15);

    robot_drivetrain.turn_to(0);

    robot_drivetrain.drive_forward(-15);
}

/**
 * My third autonomous routine
 */
void auton_3(){
    robot_drivetrain.set_pose_units(PoseUnits::in_deg_cw);
    robot_drivetrain.set_pose(25, 0, 0);

    robot_drivetrain.reverse_through_path({{15, 15, 180}, {0, 0, 180}}, 7);
}

WhoopAutonSelector auton_selector(&controller1, {
    AutonRoutine("First Auton", auton_1),
    AutonRoutine("Second Auton", auton_2),
    AutonRoutine("Third Auton", auton_3)
}, "auton.txt");

ComputeManager manager({&buffer_system, &jetson_commander, &robot_drivetrain, &controller1, &auton_selector});

/*---------------------------------------------------------------------------*/
/*                          Pre-Autonomous Functions                         */
/*                                                                           */
/*  You may want to perform some actions before the competition starts.      */
/*  Do them in the following function.  You must return from this function   */
/*  or the autonomous and usercontrol tasks will not be started.  This       */
/*  function is only called once after the V5 has been powered on and        */
/*  not every time that the robot is disabled.                               */
/*---------------------------------------------------------------------------*/
void pre_auton() {
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

void autonomous() {
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
void usercontrol() {
  robot_drivetrain.set_state(drivetrainState::mode_usercontrol);

  // User control code here, inside the loop
  while (1) {
    wait(20, msec);
  }
}

//
// Main will set up the competition functions and callbacks.
//
int main() {
  Competition.autonomous(autonomous);
  Competition.drivercontrol(usercontrol);
  pre_auton();
  while (true) wait(100, msec);
}
```

#### **PROS**

#### `main.cpp`

```cpp
// Code and additional configuration above...

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
    robot_drivetrain.set_pose_units(PoseUnits::in_deg_cw);
    robot_drivetrain.set_pose(-30, -10, 0);

    robot_drivetrain.drive_forward(15);

    robot_drivetrain.turn_to(0);

    robot_drivetrain.drive_forward(-15);
}

/**
 * My third autonomous routine
 */
void auton_3(){
    robot_drivetrain.set_pose_units(PoseUnits::in_deg_cw);
    robot_drivetrain.set_pose(25, 0, 0);

    robot_drivetrain.reverse_through_path({{15, 15, 180}, {0, 0, 180}}, 7);
}

WhoopAutonSelector auton_selector(&controller1, {
    AutonRoutine("First Auton", auton_1),
    AutonRoutine("Second Auton", auton_2),
    AutonRoutine("Third Auton", auton_3)
}, "auton.txt");

ComputeManager manager({&buffer_system, &jetson_commander, &robot_drivetrain, &controller1, &auton_selector});

/**
 * Runs initialization code. This occurs as soon as the program is started.
 *
 * All other competition modes are blocked by initialize; it is recommended
 * to keep execution time for this mode under a few seconds.
 */
void initialize() {
	pros::lcd::initialize();
	pros::lcd::set_text(1, "Hello PROS User!");
	auton_selector.run_selector();
  controller1.notify("Initializing");
  manager.start();
}

/**
 * Runs while the robot is in the disabled state of Field Management System or
 * the VEX Competition Switch, following either autonomous or opcontrol. When
 * the robot is enabled, this task will exit.
 */
void disabled() {
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
void autonomous() {
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
void opcontrol() {
	robot_drivetrain.set_state(drivetrainState::mode_usercontrol);

	while (true) {
		pros::delay(20);                               // Run for 20 ms then update
	}
}
```

<!-- tabs:end -->

