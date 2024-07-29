# JetsonCommander Commands

## reboot_jetson

This function would reboot the Jetson Nano

```clike
/**
* Restarts Jetson Nano
*/
void reboot_jetson();
```

#### Example:

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
jetson_commander.reboot_jetson();
```

<!-- tabs:end -->

---

## shutdown_jetson

```clike
/**
* Shuts down Jetson Nano
*/
void shutdown_jetson();
```

#### Example:

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
jetson_commander.shutdown_jetson();
```

<!-- tabs:end -->

---

## restart_vision_process

This restarts the processes for the Jetson Nano. It is quicker than shutting down, but may take a bit to initialize.

```clike
/**
* Restarts the vision process on Jetson Nano
*/
void restart_vision_process();
```

#### Example:

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
jetson_commander.restart_vision_process();
```

<!-- tabs:end -->

---

## initialize

This is to be used at the start of the robot's program to send an initialization message to the Jetson Nano.

```clike
/**
* Sends initialization message to Jetson Nano
*/
void initialize();
```


#### Example:

<!-- tabs:start -->

#### **VEXCode**

```cpp
void pre_auton()
{

  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();
  manager.start();
  robot_drivetrain.set_state(drivetrainState::mode_disabled);
  controller1.notify("Initializing", 2);
  jetson_commander.initialize(); // Initialize Jetson Commander
}
```

#### **PROS**

```cpp
void initialize()
{
    pros::lcd::initialize();
    pros::lcd::set_text(1, "Hello PROS User!");
    controller1.notify("Initializing");
    manager.start();
    jetson_commander.initialize(); // If you don't have Tesseract, omit this line
    robot_drivetrain.calibrate();

}

void disabled()
{
    robot_drivetrain.set_state(drivetrainState::mode_disabled);
}
```

<!-- tabs:end -->

---

## is_connected_to_jetson

Returns true if the V5 Brain is connected to the Jetson Nano and established positive contact

```clike
/**
* Returns true if connected to Jetson Nano
* @returns true if connected, false otherwise (delay 5-6 seconds)
*/
bool is_connected_to_jetson();
```

#### Example:

<!-- tabs:start -->

#### **VEXCode & PROS**

```cpp
bool is_connected = jetson_commander.is_connected_to_jetson();

if(is_connected){
    // Do something if the Jetson is connected
}
else{
    // Do another thing
}

```

<!-- tabs:end -->
