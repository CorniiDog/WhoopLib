# Creating Manager Object

So, given that we have objects, everything needs to be managed right? So that is what the manager object does.

Certain objects are nodes. That is that they are inherited from the ```ComputeNode``` class and can have a ```__step()``` method override. If this sounds a bit confusing, let me make it simpler to understand. Some objects need to be controlled and have their own sub-process in the background behind the scenes to function correctly. And that is why some objects are configured to be capable of ran by the manager to provide functions their own independance but yet still have complete control over an object's functionality.

<!-- tabs:start -->

#### **If You Have a Vision Tesseract**

<!-- tabs:start -->

#### **VEXCode**

```cpp
ComputeManager manager({&buffer_system, &jetson_commander, &robot_drivetrain, &controller1});
```

<!-- tabs:end -->

#### **If You Do Not Have a Vision Tesseract**

<!-- tabs:start -->

#### **VEXCode**

```cpp
ComputeManager manager({&robot_drivetrain, &controller1});
```

<!-- tabs:end -->

<!-- tabs:end -->


Pretty straightforward, right?

Then, in your main.cpp add the following to your ```pre_auton``` function:

<!-- tabs:start -->

#### **VEXCode**

```cpp
void pre_auton(void) {

  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();
  manager.start();
  robot_drivetrain.set_state(drivetrainState::mode_disabled);
  jetson_commander.initialize(); // If you don't have Tesseract, omit this line
  robot_drivetrain.calibrate();
}
```

<!-- tabs:end -->

Add the following to your ```autonomous``` function:

<!-- tabs:start -->

#### **VEXCode**

```cpp
void autonomous(void) {
  robot_drivetrain.set_state(drivetrainState::mode_autonomous);
}
```

<!-- tabs:end -->


Add the following to your ```usercontrol``` function:

<!-- tabs:start -->

#### **VEXCode**

```cpp
void usercontrol(void) {
  robot_drivetrain.set_state(drivetrainState::mode_usercontrol);

  while (1) {
    wait(100, msec); // Keep-alive and prevent wasted resources
  }
}
```

<!-- tabs:end -->

So, just a run-down. Your main.cpp may contain something like:

<!-- tabs:start -->

#### **VEXCode**

```cpp

/*---------------------------------------------------------------------------*/
/*                          Pre-Autonomous Functions                         */
/*                                                                           */
/*  You may want to perform some actions before the competition starts.      */
/*  Do them in the following function.  You must return from this function   */
/*  or the autonomous and usercontrol tasks will not be started.  This       */
/*  function is only called once after the V5 has been powered on and        */
/*  not every time that the robot is disabled.                               */
/*---------------------------------------------------------------------------*/
void pre_auton(void) {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();
  manager.start();
  robot_drivetrain.set_state(drivetrainState::mode_disabled);
  controller1.notify("Initializing", 2);
  jetson_commander.initialize();
  robot_drivetrain.calibrate();
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

void autonomous(void) {
  robot_drivetrain.set_state(drivetrainState::mode_autonomous);
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
void usercontrol(void) {
  robot_drivetrain.set_state(drivetrainState::mode_usercontrol);

  // User control code here, inside the loop
  while (1) {
    wait(100, msec);
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

<!-- tabs:end -->

