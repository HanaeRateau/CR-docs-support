# None

[Emio API](https://github.com/SofaComplianceRobotics/Emio.API) is a Python package created to control the [Emio robot](https://compliance-robotics.com/compliance-lab/).

To install it from PyPI, run the following command: 
```bash
pip install emioapi
```

To install the latest version from the Github repository, run:  
```bash
pip install git+https://github.com/SofaComplianceRobotics/Emio.API.git@main
```

## EmioAPI Class

```python
class EmioAPI()
```

Class to control emio motors.
It is essentially divided into two objects:
- The [`motors`](#motors) object ([`EmioMotors`](#emiomotors)), which is used to control the motors.
- The [`camera`](#camera) object ([`EmioCamera`](#emiocamera)), which is used to control the camera.


The EmioAPI class is the main class that combines both classes and provides a simple interface to control the emio device.
It also provides static utility methods to list the emio devices connected to the computer.

Motors:
> The motors are controlled in position mode. The class is thread-safe and can be used in a multi-threaded environment.
> All the data sent to the motors are list of *4 values* for the *4 motors* of the emio device. The order in the list corresponds to the motor ID's in the emio device.
> Motor 0 is the first motor in the list, motor 1 is the second motor, etc.
> You can open a connection directly to the motors using the [`open`](#opendevice_name-str--none) method of the `motors` object.
>
> :::warning
>
> Emio motors are clamped between 0 and PI radians (0 and 180 degrees). If you input a value outside this range, the motor will not move.
>
> :::

Camera:
> The camera is used to track objects and compute the point cloud.
> The camera parameters are stored in a config file. If the config file is not found, default values are used.
> The camera can be configured to show the frames, track objects, and compute the point cloud.
> You can open a connection directly to the camera using the [`open`](#open) method of the `camera` object.
>
> :::warning
> By default, EmioAPI launches the camera in the same process by creating an [EmioCamera](#emiocamera) object.
> You can launch the camera in another process using a [MultiProcessEmioCamera](#multiprocessemiocamera) by setting the `multiprocess_camera=True` when creating an `EmioAPI` object.
> :::


**Example**:

    ```python
    from emioapi import EmioAPI

    # Create an EmioAPI instance
    emio = EmioAPI(multiprocess_camera=False)

    # Connect to the first available Emio device
    if emio.connectToEmioDevice():
        print("Connected to Emio device.")

        # Print device status
        emio.printStatus()

        # Example: Move all motors to 90 degrees (PI/2 radians)
        target_angles = [math.pi/2] * 4
        emio.motors.angles = target_angles

        # Disconnect when done
        emio.disconnect()
    else:
        print("Failed to connect to Emio device.")
    ```

### motors

The emio motors object: [`EmioMotors`](#emiomotors)

### camera

The emio camera object: [`EmioCamera`](#emiocamera) | [`MultiprocessEmioCamera`](#multiprocessemiocamera)

### device\_name

```python
@property
def device_name() -> str | None
```

Get the port name to which the EmioAPI object is connected if connected, else None

### camera\_serial

```python
@property
def camera_serial() -> str | None
```

Get the camera serial number to which the EmioAPI object is connected if connected, else None

### listEmioDevices

```python
@staticmethod
def listEmioDevices() -> list
```

List all the emio devices connected to the computer.

**Returns**:

  A list of device names (the ports).

### listUnusedEmioDevices

```python
@staticmethod
def listUnusedEmioDevices() -> list
```

List all the emio devices that are not currently used by any instance of EmioAPI in this process.

**Returns**:

  A list of device names (the ports).

### listUsedEmioDevices

```python
@staticmethod
def listUsedEmioDevices() -> list
```

List all the emio devices that are currently used by an instance of EmioAPI in this process.

**Returns**:

  A list of device names (the ports).

### connectToEmioDevice

```python
def connectToEmioDevice(device_name: str = None,
                        multi_turn: bool = False) -> bool
```

Connect to the emio device with the given name.

**Arguments**:

- `device_name` - str: The name of the device to connect to. If None, the first device found that is not used will be used.
- `multi_turn` - bool: Whether to enable the multi-turn mode of the motors. In multi-turn mode on, the angles interval is [-256*2π, 256*2π]
  

**Returns**:

  True if the connection is successful, False otherwise.

### disconnect

```python
def disconnect()
```

Close the connection to motors and camera.

### printStatus

```python
def printStatus()
```

Print the status of the Emio device.

## EmioCamera Class

```python
class EmioCamera()
```

A class to interface with the Realsense camera on Emio.
This class opens the camera in the same process as the code is running from.

It is recommendend to use this class if you want to use the camera in a SOFA scene.

:::warning
If you want to open the camera in another process, you can use the [MultiprocessEmioCamera](#MultiprocessEmioCamera) class.
:::


**Example**:

    ```python
    from emioapi import EmioCamera

    # Create an instance of EmioCamera
    camera = EmioCamera(show=True, track_markers=True, compute_point_cloud=True, configuration="extended")

    # Open the camera
    if camera.open():
        try:
            while camera.is_running:
                # Update camera frames and tracking
                camera.update()

                # Access tracker positions
                positions = camera.trackers_pos
                print("Tracker positions:", positions)

                # Access point cloud data
                pc = camera.point_cloud
                print("Point cloud shape:", pc.shape)

                # Access HSV and mask frames
                hsv = camera.hsv_frame
                mask = camera.mask_frame

                # ... (process frames as needed)

                # For demonstration, break after one iteration
                break
        finally:
            # Close the camera when done
            camera.close()
    ```

### \_\_init\_\_

```python
def __init__(camera_serial: str = None,
             parameter: dict = None,
             show: bool = False,
             track_markers: bool = False,
             compute_point_cloud: bool = False,
             configuration: str = "extended")
```

Initialize the camera.

**Arguments**:

- `camera_serial` - str: The serial number of the camera to connect to. If None, the first camera found will be used.
- `parameter` - dict:  The camera parameters. If None, the lastest save paramters are used from a file, but if no file is found, default values will be used.
- `show` - bool:  Whether to show the camera HSV and Mask frames or not.
- `track_markers` - bool:  Whether to track objects or not.
- `compute_point_cloud` - bool: Whether to compute the point cloud or not.
- `configuration` - str: Configuration of Emio, either "extended" (default) or "compact"

### depth\_frame

```python
@property
def depth_frame()
```

Get the latest depth frame

**Returns**:

- `numpy.ndarray` - the latest depth frame

### frame

```python
@property
def frame()
```

Get the latest color frame

**Returns**:

- `numpy.ndarray` - the latest color frame

### is\_running

```python
@property
def is_running() -> bool
```

Get the running status of the camera.

**Returns**:

- `bool` - The running status of the camera.

### track\_markers

```python
@property
def track_markers() -> bool
```

Get whether the camera is tracking objects or not.

**Returns**:

- `bool` - True if the camera is tracking the markers, else False.

### track\_markers

```python
@track_markers.setter
def track_markers(value: bool)
```

Set the tracking status of the camera.

**Arguments**:

- `value` - bool: The new tracking status.

### compute\_point\_cloud

```python
@property
def compute_point_cloud() -> bool
```

Get whether the camera is computing the point cloud or not.

**Returns**:

- `bool` - True if the camera is computing the point cloud, else False.

### compute\_point\_cloud

```python
@compute_point_cloud.setter
def compute_point_cloud(value: bool)
```

Set the point cloud computation status of the camera.

**Arguments**:

- `value` - bool: The new point cloud computation status.

### show\_frames

```python
@property
def show_frames() -> bool
```

Get whether the camera HSV and mask frames are shown in windows.

**Returns**:

- `bool` - The show status of the camera.

### show\_frames

```python
@show_frames.setter
def show_frames(value: bool)
```

Set the show status of the camera.

**Arguments**:

- `value` - bool: The new show status.

### parameters

```python
@property
def parameters() -> dict
```

Get the camera parameters in a dict object:
- `hue_h`: int: The upper hue value.
- `hue_l`: int: The lower hue value.
- `sat_h`: int: The upper saturation value.
- `sat_l`: int: The lower saturation value.
- `value_h`: int: The upper value value.
- `value_l`: int: The lower value value.
- `erosion_size`: int: The size of the erosion kernel.
- `area`: int: The minimum area of the detected objects.

**Returns**:

- `dict` - The camera parameters.

### parameters

```python
@parameters.setter
def parameters(value: dict)
```

Set the camera tracking parameters from the dict object:
- `hue_h`: int: The upper hue value.
- `hue_l`: int: The lower hue value.
- `sat_h`: int: The upper saturation value.
- `sat_l`: int: The lower saturation value.
- `value_h`: int: The upper value value.
- `value_l`: int: The lower value value.
- `erosion_size`: int: The size of the erosion kernel.
- `area`: int: The minimum area of the detected objects.

:::warning
- The camera parameters are not saved to a file. You need to save them manually.
- The paramters are set when opening the camera. To change the parameters programatically, you need to close the camera and open it again with the wanted parameters.
:::

**Arguments**:

- `value` - dict: The new camera parameters.

### trackers\_pos

```python
@property
def trackers_pos() -> list
```

Get the positions of the trackers.

**Returns**:

- `list` - The positions of the trackers as a list of lists.

### trackers\_pos\_image

```python
@property
def trackers_pos_image() -> list
```

Get the positions of the trackers in the image frame.

**Returns**:

- `list` - The positions of the trackers in the image frame as a list of lists.

### point\_cloud

```python
@property
def point_cloud() -> np.ndarray
```

Get the point cloud data.

**Returns**:

  The point cloud data as a numpy array.

### hsv\_frame

```python
@property
def hsv_frame() -> np.ndarray
```

Get the HSV frame.

**Returns**:

  The HSV frame as a numpy array.

### mask\_frame

```python
@property
def mask_frame() -> np.ndarray
```

Get the mask frame.

**Returns**:

  The mask frame as a numpy array.

### calibration\_status

```python
@property
def calibration_status() -> int
```

Get the calibration status of the camera.

**Returns**:

- `int` - The calibration status of the camera. -1 if camera is None

### fps

```python
@property
def fps() -> int
```

Get the current stream framerate of the camera in frames per second.
Default is 30 fps.

You have to set the fps _before_ calling the `open` method.

**Returns**:

- `int` - The framerate in fps

### fps

```python
@fps.setter
def fps(value: int)
```

Set the camera framerate.
Available framerates are 30, 60 and 90 fps.
Default is 30 fps.

### depth\_max

```python
@property
def depth_max() -> int
```

Get the maximum depth value of the camera in millimiters.
Default is 430 mm.

**Returns**:

- `int` - The maximum depth value in millimiters.

### depth\_max

```python
@depth_max.setter
def depth_max(value: int)
```

Set the maximum depth value of the camera in millimiters.
Must be positive.
Default is 430 mm.

**Arguments**:

- `value` - int: The new maximum depth value in millimmiters.

### depth\_min

```python
@property
def depth_min() -> int
```

Get the minimum depth value of the camera in millimiters.
Default is 2 mm.

**Returns**:

- `int` - The minimum depth value in millimiters.

### depth\_min

```python
@depth_min.setter
def depth_min(value: int)
```

Set the minimum depth value of the camera in millimiters.
Must be positive.
Default is 2 mm.

**Arguments**:

- `value` - int: The new minimum depth value in millimmiters.

### listCameras

```python
@staticmethod
def listCameras() -> list
```

Static method to list all the Realsense cameras connected to the computer

**Returns**:

- `list` - A list of the serial numbers as string.

### open

```python
def open(camera_serial: str = None) -> bool
```

Initialize and open the camera in another process.
This function creates a new handle to the camera and starts it.

**Arguments**:

- `camera_serial` - str: the serial number of the camera to open. If None, the first found Realsense camera will be opened. If the `camera_serial` was set as a parameter or before, the given camera will be opened.
  

**Returns**:

- `bool` - True if a camera was opened, else False

### calibrate

```python
def calibrate()
```

Calibrate the camera. You need to set up Emio in the calibration configuration before calling this method.
See the [Emio documentation](https://docs-support.compliance-robotics.com/docs/next/Users/Emio/getting-started-with-emio/).

### image\_to\_simulation

```python
def image_to_simulation(x: int, y: int, depth: float = None) -> list[float]
```

Get the 3D point in the simulation reference frame from the pixels and depth

**Arguments**:

  x, y: int: the horizontal and vertical position in the image/frame
  

**Returns**:

  a list of float of the corresponding 3D point in the simulation reference frame

### update

```python
def update()
```

Update the camera frames and tracking elements (markers and point cloud)

### close

```python
def close()
```

Close the camera and terminate the process. Sets the running status to False.

## EmioMotors Class

```python
class EmioMotors(DynamixelMotors)
```

Class to control emio motors.
The class is designed to be used with the emio device.
The motors are controlled in position mode. The class is thread-safe and can be used in a multi-threaded environment.

It is a wrapper around the DynamixelMotors class from the dynamixelmotorsapi package, with some specific configurations for the emio device.
You can find the doccumentation of the DynamixelMotors class in the [dynamixelmotorsapi](./dynamixelmotors-api.md) package.

**Example**:

    ```python
    from emioapi import EmioMotors

    # Create an instance of EmioMotors
    motors = EmioMotors()

    # Open connection to the motors (optionally specify device name)
    if motors.open():
        # Print current angles in radians
        print("Current angles (rad):", motors.angles)

        # Set new goal angles (example values)
        motors.angles = [0.5, 1.0, -0.5, 1.0]

        # Print status
        motors.printStatus()

        # Close connection when done
        motors.close()
    else:
        print("Failed to connect to motors.")
    ```

## MultiprocessEmioCamera Class

```python
class MultiprocessEmioCamera()
```

A class to interface with the realsense camera on Emio.
This class creates a process using mulltiprocessing to handle the camera.

:::warning
This class does **not** work in a SOFA scene. The multiprocessing clashes with SOFA.
If you want to use the camera in a SOFA scene, use the not-paralelized version of the class: [EmioCamera](#EmioCamera)
:::

**Example**:

    ```python
    from emioapi import MultiprocessEmioCamera

    # Initialize the camera with default parameters
    camera = MultiprocessEmioCamera(show=True, tracking=True, compute_point_cloud=True)

    # Open the camera (starts the camera process)
    if camera.open():
        print("Camera started successfully.")

        # Access tracker positions and point cloud in a loop
        for _ in range(10):
            print("Trackers positions:", camera.trackers_pos)
            print("Point cloud shape:", camera.point_cloud.shape if camera.point_cloud is not None else None)
            time.sleep(1)

        # Close the camera process
        camera.close()
        print("Camera closed.")
    else:
        print("Failed to start camera.")
    ```

### \_\_init\_\_

```python
def __init__(camera_serial=None,
             parameter=None,
             show=False,
             tracking=True,
             compute_point_cloud=False)
```

Initialize the camera.

**Arguments**:

- `camera_name` - str: The name of the camera to connect to. If None, the first camera found will be used.
- `parameter` - dict:  The camera parameters. If None, the lastest save paramters are used from a file, but if no file is found, default values will be used.
- `show` - bool:  Whether to show the camera HSV and Mask frames or not.
- `tracking` - bool:  Whether to track objects or not.
- `compute_point_cloud` - bool: Whether to compute the point cloud or not.

### camera\_serial

```python
@property
def camera_serial() -> str
```

Get the current camera serial number

### is\_running

```python
@property
def is_running() -> bool
```

Get the running status of the camera.

**Returns**:

- `bool` - The running status of the camera.

### track\_markers

```python
@property
def track_markers() -> bool
```

Get whether the camera is tracking objects or not.

**Returns**:

- `bool` - True if the camera is tracking the markers, else False.

### track\_markers

```python
@track_markers.setter
def track_markers(value: bool)
```

Set the tracking status of the camera.

**Arguments**:

- `value` - bool: The new tracking status.

### compute\_point\_cloud

```python
@property
def compute_point_cloud() -> bool
```

Get whether the camera is computing the point cloud or not.

**Returns**:

- `bool` - True if the camera is computing the point cloud, else False.

### compute\_point\_cloud

```python
@compute_point_cloud.setter
def compute_point_cloud(value: bool)
```

Set the point cloud computation status of the camera.

**Arguments**:

- `value` - bool: The new point cloud computation status.

### show\_frames

```python
@property
def show_frames() -> bool
```

Get the show status of the camera.

**Returns**:

- `bool` - The show status of the camera.

### show\_frames

```python
@show_frames.setter
def show_frames(value: bool)
```

Set the show status of the camera.

**Arguments**:

- `value` - bool: The new show status.

### parameters

```python
@property
def parameters() -> dict
```

Get the camera parameters in a dict:
- `hue_h`: int: The upper hue value.
- `hue_l`: int: The lower hue value.
- `sat_h`: int: The upper saturation value.
- `sat_l`: int: The lower saturation value.
- `value_h`: int: The upper value value.
- `value_l`: int: The lower value value.
- `erosion_size`: int: The size of the erosion kernel.
- `area`: int: The minimum area of the detected objects.

**Returns**:

- `dict` - The camera parameters.

### parameters

```python
@parameters.setter
def parameters(value: dict)
```

Set the camera tracking parameters from the dict:
- `hue_h`: int: The upper hue value.
- `hue_l`: int: The lower hue value.
- `sat_h`: int: The upper saturation value.
- `sat_l`: int: The lower saturation value.
- `value_h`: int: The upper value value.
- `value_l`: int: The lower value value.
- `erosion_size`: int: The size of the erosion kernel.
- `area`: int: The minimum area of the detected objects.

:::warning
- The camera parameters are not saved to a file. You need to save them manually.
- The paramters are set when opening the camera. To change the parameters programatically, you need to close the camera and open it again with the wanted parameters.
:::

**Arguments**:

- `value` - dict: The new camera parameters.

### trackers\_pos

```python
@property
def trackers_pos() -> list
```

Get the positions of the trackers.

**Returns**:

- `list` - The positions of the trackers as a list of lists.

### point\_cloud

```python
@property
def point_cloud() -> np.ndarray
```

Get the point cloud data.

**Returns**:

  The point cloud data as a numpy array.

### hsv\_frame

```python
@property
def hsv_frame() -> np.ndarray | None
```

Get the HSV frame.

**Returns**:

  The HSV frame as a numpy array.

### mask\_frame

```python
@property
def mask_frame() -> np.ndarray | None
```

Get the mask frame.

**Returns**:

  The mask frame as a numpy array.

### listCameras

```python
@staticmethod
def listCameras()
```

Static method to list all the Realsense cameras connected to the computer

**Returns**:

- `list` - A list of the serial numbers as string.

### \_\_getstate\_\_

```python
def __getstate__()
```

Get the state of the object for pickling.
This method is used to remove the _manager attribute from the object state based on https://laszukdawid.com/blog/2017/12/13/multiprocessing-in-python-all-about-pickling/

### open

```python
def open(camera_serial: str = None) -> bool
```

Initialize and open the camera in another process.
This function creates a new process to handle the camera and starts it.

### close

```python
def close()
```

Close the camera and terminate the process. Sets the running status to False.

## UDPBridgeConfig Class

```python
@dataclass
class UDPBridgeConfig()
```

### fps

the required FPS of transfer

### nb\_markers

number of markers to track

### side

"plane", "top" or "front"

### sort

"y" or "z", only for front camera

### remote\_ip

IP address of the remote host

### remote\_port

UDP port Remote host listens on (Python -> Remote host)

### local\_port

UDP port Python listens on (Remote host -> Python)

### bind\_port

Local port used for sending

### recv\_timeout

Socket timeout in seconds while waiting for a reply

### camera\_only

Only send camera data, the motors command will be sent as 0 and no command will be applied to the motors

### motors\_only

Only send motor data, the markers position will be sent as 0 and the camera will not be read

## CommStatus Class

```python
class CommStatus(Enum)
```

Status returned by :meth:`UDPBridge.send_and_receive`.

**Attributes**:

- `OK` - Reply received, sequence numbers match (one-tick delay).
- `OK_NO_DELAY` - Reply received with no delay (seq matches exactly).
- `DESYNC` - Sequence mismatch detected; bridge is flushing the buffer.
- `TIMEOUT` - No reply received within ``recv_timeout``.
- `RECONNECTED` - Bridge lost sync and successfully re-handshaked.

## UDPBridge Class

```python
class UDPBridge()
```

UDP bridge between Python and a Remote host (e.g. Simulink) for real-time control.

The bridge consists in two processes running in parallel: one for the camera and one for the motors:
- The camera process reads the camera frames, tracks the markers and updates a shared variable with the markers position.
- The motors process waits for the camera process to update the markers position, then it reads the motors position, sends both the motors position and the markers position to the remote host and applies the received command to the motors.


The UDPBridge is the clock master: it sends a vector of ``send_size`` doubles at
each tick (prepended with a sequence number) and blocks until Remote host
replies with a vector of ``recv_size`` doubles (also prepended with a
sequence number).

Wire format (both directions)::

[ seq (float64) | data[0] | data[1] | ... ]
total bytes = (1 + send_size) * 8   (Python -> Remote host)
total bytes = (1 + recv_size) * 8   (Remote host -> Python)

Not thread-safe: ``send_and_receive`` must be called from a single thread.

Typical usage::

with UDPBridge(send_size=3, recv_size=2) as bridge:
bridge.handshake()
while True:
reply, status = bridge.send_and_receive(my_data)

**Arguments**:

- `send_size` - Number of data doubles sent to UDP each tick.
- `recv_size` - Number of data doubles expected from UDP each tick.
- `remote_ip` - IP address of the UDP host.
- `remote_port` - UDP port Remote host listens on (Python -> Remote host).
- `local_port` - UDP port Python listens on (Remote host -> Python).
- `bind_port` - Local port used for sending.
- `recv_timeout` - Socket timeout in seconds while waiting for a reply.

### handshake

```python
def handshake(handshake_timeout: float = 0.05) -> None
```

Block until Remote host acknowledges the connection.

Sends ``seq=-1`` with a zero payload of ``send_size`` doubles so the
wire size is identical to the main loop. Resets the sequence counter
on success.

**Arguments**:

- `handshake_timeout` - Per-attempt socket timeout in seconds.

### send\_and\_receive

```python
def send_and_receive(data: np.ndarray) -> tuple[np.ndarray, CommStatus]
```

Send a data vector and return the reply from Remote host.

``data`` is silently padded with zeros or truncated to match
``send_size`` if needed (a warning is emitted).

On consecutive timeouts (>= ``MAX_RECONNECT_ATTEMPTS``) or persistent
desyncs (>= ``_MAX_DESYNC_COUNT``), a new handshake is triggered
automatically.

**Arguments**:

- `data` - Vector of doubles, ideally shape ``(send_size,)`` or
  ``(send_size, 1)``.
  

**Returns**:

  A tuple ``(reply, status)`` where ``reply`` has shape
  ``(recv_size, 1)`` and ``status`` is one of:
  :class:`CommStatus`.
  

**Raises**:

- `RuntimeError` - If the packet received from Remote host has an
  unexpected size (mismatch between ``recv_size`` and the
  Remote host UDP Send block configuration).

### close

```python
def close() -> None
```

Release UDP sockets.

### seq

```python
@property
def seq() -> int
```

Current sequence counter.

### process\_motors

```python
def process_motors(shared_markers_pos: SynchronizedArray, event_frame: Event,
                   event_measure: Event, config: UDPBridgeConfig) -> None
```

Motor control loop bridging the remote controller and the physical motors.

Waits for frame and measure events, reads motor positions and marker data,
then exchanges them with the remote host to get the next command. Runs until
interrupted by a KeyboardInterrupt (Ctrl-C).

**Arguments**:

- `shared_markers_pos` - Shared memory array holding marker positions.
- `event_frame` - Event set by the camera process at each new frame.
- `event_measure` - Event set when marker measurement is ready.

### setup\_motors

```python
def setup_motors() -> EmioMotors
```

Open and return an EmioMotors instance, retrying until successful.

**Returns**:

  An open EmioMotors instance.

### send\_motors\_command

```python
def send_motors_command(motors: EmioMotors, command: np.ndarray) -> None
```

Send a command vector to the motors.

**Arguments**:

- `motors` - An open EmioMotors instance.
- `command` - Command vector, any shape — will be flattened.

### get\_motors\_position

```python
def get_motors_position(motors: EmioMotors) -> np.ndarray
```

Read current motor angles.

**Arguments**:

- `motors` - An open EmioMotors instance.
  

**Returns**:

  Motor positions as a column vector, shape ``(n_motors, 1)``.

### process\_camera

```python
def process_camera(shared_markers_pos: SynchronizedArray, event_frame: Event,
                   event_measure: Event, config: UDPBridgeConfig) -> None
```

Main camera loop: grab frames, track markers, and update shared state.

Sets ``event_frame`` at each new frame and ``event_measure`` once the
marker positions are written to ``shared_markers_pos``.
Stops when the user presses ``q``.

**Arguments**:

- `shared_markers_pos` - Shared memory array written with marker positions.
- `event_frame` - Event set at each new camera frame.
- `event_measure` - Event set once marker data is ready.

### setup\_camera

```python
def setup_camera(config: UDPBridgeConfig) -> EmioCamera
```

Initialise and open the depth camera from ``params``.

**Returns**:

  A configured, open DepthCamera instance.

### process\_frame

```python
def process_frame(camera: EmioCamera, last_pos: np.ndarray,
                  config: UDPBridgeConfig) -> np.ndarray
```

Extract marker positions from the current frame.

Returns ``last_pos`` unchanged if the expected number of markers is not
detected.

**Arguments**:

- `camera` - An open, tracking-enabled DepthCamera instance.
- `last_pos` - Position array returned on detection failure.
  

**Returns**:

  Marker positions as a column vector, shape ``(ny, 1)``.

### pixel\_to\_mm

```python
def pixel_to_mm(points: np.ndarray, depth: float,
                config: UDPBridgeConfig) -> np.ndarray
```

Project pixel coordinates to millimetres using pinhole intrinsics.

**Arguments**:

- `points` - Tracker positions in pixels, shape ``(n, 3)``.
- `depth` - Fixed depth in mm used for the projection.
  

**Returns**:

  Projected points in mm, shape ``(n, 3)``.

### camera\_to\_sofa\_order

```python
def camera_to_sofa_order(points: np.ndarray,
                         config: UDPBridgeConfig) -> np.ndarray
```

Reorder markers by ascending y-coordinate (SOFA convention).

**Arguments**:

- `points` - Marker positions, shape ``(nb_markers, 3)``.
  

**Returns**:

  Reordered positions as a flat array.

### startUDPbridge

```python
def startUDPbridge(config: UDPBridgeConfig)
```

Start a UDP bridge configured with the parameters found in args Start a UDP bridge configured with the parameters found in args or will default to `emioapi/udpbridge/udpbridge_params.py`.

The bridge consists in two processes running in parallel: one for the camera and one for the motors:
    - The camera process reads the camera frames, tracks the markers and updates a shared variable with the markers position. 
    - The motors process waits for the camera process to update the markers position, then it reads the motors position, sends both the motors position and the markers position to the remote host and applies the received command to the motors.

A handshake is done at the beginning to ensure that the remote host is ready to receive data. It shold follow the same protocol describded below with dummy data.

The protocol is as follows:
- The bridge sends a packet made of a sequence number, the four motors positions and followed by the marker(s) position(s)
- The remote host should reply with a packet containing the four motors positions to send to the Emio robot.

### side

"top", "front", "plane"

### sort

"y" or "z", only for front camera

### ny

number of measurements

### nu

number of actuators

### depth

for front camera,

## Emio API Tools

This module provides command-line tools for working with Emio devices, including camera 
calibration and starting a UDP bridge for real-time communication between the Emio robot 
and a remote host (e.g., Simulink).

To use these tools, run the following command in your terminal:
```bash
python -m emioapi <command> [options]
```
### Available Commands
- `calibrate`: Calibrate the Emio camera. This command will open the camera feed
    and allow you to perform the calibration process. The camera will be automatically closed after calibration.
- `startUDP`: Start a UDP bridge for motor/camera data. 
    This command will launch a UDP bridge that sends the camera's markers and motors positions and receives motor commands from a remote host. 
    The bridge can be configured with various options such as FPS, number of markers, remote IP/port, etc.

You can run each command with the `--help` flag to see the specific options available for that command. For example:
```bash
python -m emioapi --help
python -m emioapi calibrate --help
python -m emioapi startUDP --help
```

### calibrate

```python
def calibrate()
```

Calibrate the camera of the first Emio camera found. For more informations about the calibration process, please refer to the EmioCamera.calibrate() method documentation.

### startUDP

```python
def startUDP(args)
```

Start a UDP bridge configured with the parameters found in args or will default to `emioapi/udpbridge/udpbridge_params.py`.

A handshake is done at the beginning to ensure that the remote host is ready to receive data. It should follow the same protocol describded below with dummy data.

The sequence number is a simple counter that is incremented at each frame. It is used by the process_motors process to make sure that the remote is synchronized.

The protocol is as follows:
- The bridge sends a packet made of a sequence number, the four motors positions and followed by the marker(s) position(s) (x, y, z)
- The remote host should reply with a packet containing the four motors positions to send to the Emio robot.

