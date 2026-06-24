# Dynamixel Motors API

This module provides an API for controlling Dynamixel motors using FTDI devices. 

You can list the FTDI devices connected to your computer using the following functions:
- `dynamixelmotorsapi.listFTDIDevices()`: Lists all FTDI devices connected to the computer.
- `dynamixelmotorsapi.listUnusedFTDIDevices()`: Lists FTDI devices that are not currently used by any instance of DynamixelMotors in this process.
- `dynamixelmotorsapi.listUsedFTDIDevices()`: Lists FTDI devices that are currently used by an instance of DynamixelMotors in this process.

### listFTDIDevices

```python
@staticmethod
def listFTDIDevices() -> list
```

List all the FTDI devices connected to the computer.

**Returns**:

  A list of device names (the ports).

### listUnusedFTDIDevices

```python
@staticmethod
def listUnusedFTDIDevices() -> list
```

List all the FTDI devices that are not currently used by any instance of DynamixelMotors in this process.

**Returns**:

  A list of device names (the ports).

### listUsedFTDIDevices

```python
@staticmethod
def listUsedFTDIDevices() -> list
```

List all the FTDI devices that are currently used by an instance of DynamixelMotors in this process.

**Returns**:

  A list of device names (the ports).

[DynamixelMotorsAPI](https://github.com/SofaComplianceRobotics/DynamixelMotorsAPI) is a Python API 
to control Dynamixel motors, supporting heterogeneous motor groups where each motor can be a 
different series with different conversion parameters.

To install the API, run:
```bash
pip install https://github.com/SofaComplianceRobotics/DynamixelMotorsAPI@main
```

The motor group is configured via a list of MotorConfig objects, one per motor, which can be loaded from a dict or JSON file.

## DynamixelMotors Class

```python
class DynamixelMotors()
```

Abstract class to control Dynamixel motors, supporting heterogeneous motor groups
where each motor can be a different series with different conversion parameters.

The motor group is configured via a list of MotorConfig objects, one per motor,
which can be loaded from a dict or JSON file.

All the motors baud rates should be the same, an exception will raise if not

**Example**:

    ```python
    from dynamixelmotorsapi import DynamixelMotors

    motors = DynamixelMotors.from_json("my_motors.json")

    if motors.open():
        print("Current angles (rad):", motors.angles)
        motors.angles = [0.5, 1.0, -0.5, 1.0]
        motors.printStatus()
        motors.close()
    else:
        print("Failed to connect to motors.")
    ```
  
  JSON format examples:
    ```json
    [
        {
            "id": 0,
            "model": "XM430-W210",
            "pulley_radius": 20,  # radius of the pulley in mm
            "pulse_center": 2048,
            "max_vel": 1000,
            "baud_rate": 57600
        },
        {
            "id": 1,
            "model": "P_SERIES",
            "pulley_radius": 30,  # radius of the pulley in mm
            "pulse_center": 0,
            "max_vel": 500,
            "baud_rate": 57600
        }
    ]
    ```
  
    ```json
    {
        "id": [0, 1],
        "model": ["XM430-W210", "P_SERIES"],
        "pulley_radius": [20, 30],
        "pulse_center": [2048, 0],
        "max_vel": [1000, 500],
        "baud_rate": 57600
    }
    ```

### listMotorsModels

```python
@staticmethod
def listMotorsModels() -> list
```

List the models of Dynamixel motors supported by this API.

### from\_dicts

```python
@classmethod
def from_dicts(cls, data: list) -> "DynamixelMotors"
```

Instantiate from a list of per-motor config dicts.

**Arguments**:

- `data` - list of dicts, each containing the fields for one MotorConfig.
  

**Returns**:

  A configured DynamixelMotors instance (not yet connected).
  

**Example**:

```json
[
    {
        "id": 0,
        "model": "XM430-W210",
        "pulley_radius": 20,  # radius of the pulley in mm
        "pulse_center": 2048,
        "max_vel": 1000,
        "baud_rate": 57600
    },
    {
        "id": [1, 2],
        "model": ["XM430-W210", "P_SERIES"],
        "pulley_radius": [0.05, 0.03],
        "pulse_center": [0, 0],
        "max_vel": [1000, 500],
        "baud_rate": 57600
    }
]
```

### from\_dict

```python
@classmethod
def from_dict(cls, data: dict) -> "DynamixelMotors"
```

Instantiate from a list of per-motor config dicts.

**Arguments**:

- `data` - dict of lists, each list containing the fields for the MotorConfig.
  

**Returns**:

  A configured DynamixelMotors instance (not yet connected).
  

**Example**:

```json
 {
    "id": 0,
    "model": "XM430-W210",
    "pulley_radius": 20, # radius of the pulley in mm
    "pulse_center": 2048,
    "max_vel": 1000,
    "baud_rate": 57600
}

OR

{
    "id": [0, 1],
    "model": ["XM430-W210", "P_SERIES"],
    "pulley_radius": [20, 30],
    "pulse_center": [2048, 0],
    "max_vel": [1000, 500],
    "baud_rate": 57600
}
```

### from\_json

```python
@classmethod
def from_json(cls, path: str) -> "DynamixelMotors"
```

Instantiate from a JSON file containing a list of per-motor config dicts.

**Arguments**:

- `path` - path to the JSON file.
  

**Returns**:

  A configured DynamixelMotors instance (not yet connected).

### lengthToPulse

```python
def lengthToPulse(displacement: list) -> list
```

Convert length (mm) to pulse, per motor.

**Arguments**:

- `displacement` - list of length values in mm, one per motor.
  

**Returns**:

  A list of pulse values for each motor.

### pulseToLength

```python
def pulseToLength(pulse: list) -> list
```

Convert pulse to length (mm), per motor.

**Arguments**:

- `pulse` - list of pulse integer values, one per motor.
  

**Returns**:

  A list of length values in mm for each motor.

### pulseToRad

```python
def pulseToRad(pulse: list) -> list
```

Convert pulse to radians, per motor.

**Arguments**:

- `pulse` - list of pulse integer values, one per motor.
  

**Returns**:

  A list of angles in radians for each motor.

### pulseToDeg

```python
def pulseToDeg(pulse: list) -> list
```

Convert pulse to degrees, per motor.

**Arguments**:

- `pulse` - list of pulse values, one per motor.
  

**Returns**:

  A list of angles in degrees for each motor.

### open

```python
def open(device_name: str = None, multi_turn: bool = False) -> bool
```

Open the connection to the motors.

**Arguments**:

- `device_name` - if set, connect to this specific port; otherwise use the first available.
- `multi_turn` - enable multi-turn mode. Angle interval becomes [-256*2π, 256*2π].

### findAndOpen

```python
def findAndOpen(device_name: str = None, multi_turn: bool = False) -> int
```

Iterate over serial ports and connect to the first available FTDI device.

**Arguments**:

- `device_name` - if set, try only this port.
- `multi_turn` - enable multi-turn mode.
  

**Returns**:

  Index of the connected port, or -1 if no connection was possible.

### emergency\_stop

```python
def emergency_stop() -> None
```

Immediately disable torque on all motors.
Does NOT close the serial port — call close() afterwards if needed.

### close

```python
def close()
```

Close the connection to the motors.

### printStatus

```python
def printStatus()
```

Print the current position of the motors in radians, pulses, and degrees.

### printConfig

```python
def printConfig()
```

Print the current configuration of the motors.

### current\_to\_torque

```python
def current_to_torque(currents_mA: List[float] | float,
                      motor_idx: int = None) -> List[float] | float
```

Estimate torque (N·mm) from the measured currents (mA) using the polynomial
T(I) fitted for the model of the given motor index.

The fit is quadratic in N·m (I in Amperes); the result is converted to N·mm.
Returns 0 for currents below the no-load threshold.

**Arguments**:

- `current_mA` - signed current in milliamps (as returned by getCurrent_mA).
- `motor_idx` - index into the active motor list (used to look up the motor model).
  

**Returns**:

  Estimated torque(s) in N·mm (always >= 0).

### torque

```python
@property
def torque() -> list
```

Get the current torque status of the motors.

### torque

```python
@torque.setter
def torque(enable: bool)
```

Enable or disable torque for the motors.

### goal\_angles

```python
@property
def goal_angles() -> list
```

Get the last commanded angles of the motors in radians.

### angles

```python
@property
def angles() -> list
```

Get the current angles of the motors in radians.

### angles

```python
@angles.setter
def angles(angles: list)
```

Set the goal angles of the motors in radians.

### goal\_positions

```python
@property
def goal_positions() -> list
```

Get the last commanded positions of the motors in pulses.

### positions

```python
@property
def positions() -> list
```

Get the current positions of the motors in pulses.

### positions

```python
@positions.setter
def positions(positions: list)
```

Set the goal positions of the motors in pulses.

### goal\_velocities

```python
@property
def goal_velocities() -> list
```

Get the last commanded velocity (rev/min) for each motor.

### goal\_velocities

```python
@goal_velocities.setter
def goal_velocities(velocities: list)
```

Set the goal velocity (rev/min) for each motor.

### goal\_pwms

```python
@property
def goal_pwms() -> list
```

Get the last commanded PWM for each motor.

### pwms

```python
@property
def pwms() -> list
```

Get the current PWM of the motors.

### pwms

```python
@pwms.setter
def pwms(pwms: list)
```

Set the goal PWM for each motor.

### max\_velocity

```python
@property
def max_velocity() -> list
```

Get the maximum velocity profile (rev/min) for each motor.

### max\_velocity

```python
@max_velocity.setter
def max_velocity(max_vel: list)
```

Set the maximum velocity profile (rev/min) in position mode, per motor.

**Arguments**:

- `max_vel` - list of maximum velocities for each motor in rev/min.

### position\_p\_gain

```python
@property
def position_p_gain() -> list
```

Get the current position P gains of the motors.

### position\_p\_gain

```python
@position_p_gain.setter
def position_p_gain(p_gains: list)
```

Set the position P gains of the motors.

### position\_i\_gain

```python
@property
def position_i_gain() -> list
```

Get the current position I gains of the motors.

### position\_i\_gain

```python
@position_i_gain.setter
def position_i_gain(i_gains: list)
```

Set the position I gains of the motors.

### position\_d\_gain

```python
@property
def position_d_gain() -> list
```

Get the current position D gains of the motors.

### position\_d\_gain

```python
@position_d_gain.setter
def position_d_gain(d_gains: list)
```

Set the position D gains of the motors.

### velocity\_profile

```python
@property
def velocity_profile() -> list
```

Get the velocity profile (rev/min) of the motors.

### velocity\_profile

```python
@velocity_profile.setter
def velocity_profile(profile: list)
```

Set the velocity profile (rev/min) of the motors.

### currents

```python
@property
def currents() -> list
```

Get the current (mA) of the motors.

### currents

```python
@currents.setter
def currents(currents: list)
```

Set the current (mA) of the motors.

### temp\_limits

```python
@property
def temp_limits()
```

Get the temperature limit set for the motors

### temp\_limits

```python
@temp_limits.setter
def temp_limits(max_temp: list)
```

Set the maximum temperature of the motors before an overheating error is set

### motor\_configs

```python
@property
def motor_configs() -> List[MotorConfig]
```

Get the list of per-motor configurations.

### is\_connected

```python
@property
def is_connected() -> bool
```

Check if the motors are connected.

### device\_name

```python
@property
def device_name() -> str
```

Get the name of the connected device port.

### device\_index

```python
@property
def device_index() -> int
```

Get the index of the device in the list of available motor devices.

### moving

```python
@property
def moving() -> list
```

Check if the motors are moving.

### moving\_status

```python
@property
def moving_status() -> list
```

Get the moving status byte of the motors.

See https://emanual.robotis.com/docs/en/dxl/x/xc330-t288/[`moving`](#dynamixelmotorsapi.dynamixelmotors.DynamixelMotors.moving)-status for details.

### velocity

```python
@property
def velocity() -> list
```

Get the current velocity (rev/min) of the motors.

### velocity\_trajectory

```python
@property
def velocity_trajectory() -> list
```

Get the velocity (rev/min) trajectory of the motors.

### position\_trajectory

```python
@property
def position_trajectory() -> list
```

Get the position (pulse) trajectory of the motors.

### temperatures

```python
@property
def temperatures() -> list
```

Get the temperature of the motors

