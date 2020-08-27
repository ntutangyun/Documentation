# Example JSON Configuration for an Autoware Auto Vehicle [](#top)

### Bridge Type [[top]] {: #bridge-type data-toc-label='Bridge Type'}

`ROS2`

### Published Topics [[top]] {: #published-topics data-toc-label='Published Topics'}

|Topic|Sensor Name|
|:-:|:-:|
|`/lgsvl/state_report`|CAN Bus|
|`/gnss/fix`|GPS|
|`/lgsvl/gnss_odom`|GPS Odometry|
|`/imu/imu_raw`|IMU|
|`/lidar_front/points_raw`|LidarFront|
|`/lidar_rear/points_raw`|LidarRear|
|`/lgsvl/clock`|Simulation Clock|

### Subscribed Topics [[top]] {: #subscribed-topics data-toc-label='Subscribed Topics'}

|Topic|Sensor Name|
|:-:|:-:|
|`/lgsvl/vehicle_control_cmd`|Autoware Car Control|
|`/lgsvl/vehicle_state_cmd`|Autoware Auto Vehicle State|

### Complete JSON Configuration [[top]] {: #complete-json-configuration data-toc-label='Complete JSON Configuration'}
```JSON
[
  {
    "type": "Transform",
    "name": "base_link",
    "transform": {
      "x": -0.015,
      "y": 0.369,
      "z": -1.37,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  },
  {
    "type": "CAN-Bus",
    "name": "CAN Bus",
    "params": {
      "Frequency": 10,
      "Topic": "/lgsvl/state_report"
    },
    "transform": {
      "x": 0,
      "y": 0,
      "z": 0,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  },
  {
    "type": "GPS Device",
    "name": "GPS",
    "params": {
      "Frequency": 12.5,
      "Topic": "/gnss/fix",
      "Frame": "gnss",
      "IgnoreMapOrigin": true
    },
    "parent": "base_link",
    "transform": {
      "x": 0,
      "y": 0,
      "z": 0,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  },
  {
    "type": "GPS Odometry",
    "name": "GPS Odometry",
    "params": {
      "Frequency": 30.0,
      "Topic": "/lgsvl/gnss_odom",
      "Frame": "odom",
      "ChildFrame": "base_link",
      "IgnoreMapOrigin": true
    },
    "parent": "base_link",
    "transform": {
      "x": 0,
      "y": 0,
      "z": 0,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  },
  {
    "type": "IMU",
    "name": "IMU",
    "params": {
      "Topic": "/imu/imu_raw",
      "Frame": "imu"
    },
    "parent": "base_link",
    "transform": {
      "x": 0,
      "y": 0,
      "z": 0,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  },
  {
    "type": "Lidar",
    "name": "LidarFront",
    "params": {
      "LaserCount": 16,
      "MinDistance": 2.0,
      "MaxDistance": 100,
      "RotationFrequency": 10,
      "MeasurementsPerRotation": 360,
      "FieldOfView": 20,
      "CenterAngle": 0,
      "Compensated": true,
      "PointColor": "#ff000000",
      "Topic": "/lidar_front/points_raw",
      "Frame": "lidar_front"
    },
    "parent": "base_link",
    "transform": {
      "x": 0.022,
      "y": 1.49,
      "z": 1.498,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  },
  {
    "type": "Lidar",
    "name": "LidarRear",
    "params": {
      "LaserCount": 16,
      "MinDistance": 2.0,
      "MaxDistance": 100,
      "RotationFrequency": 10,
      "MeasurementsPerRotation": 360,
      "FieldOfView": 20,
      "CenterAngle": 0,
      "Compensated": true,
      "PointColor": "#ff000000",
      "Topic": "/lidar_rear/points_raw",
      "Frame": "lidar_rear"
    },
    "parent": "base_link",
    "transform": {
      "x": 0.022,
      "y": 1.49,
      "z": 0.308,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  },
  {
    "type": "Vehicle Control",
    "name": "Autoware Car Control",
    "params": {
      "Topic": "/lgsvl/vehicle_control_cmd"
    }
  },
  {
    "type": "Vehicle State",
    "name": "Autoware Auto Vehicle State",
    "params": {
      "Topic": "/lgsvl/vehicle_state_cmd"
    }
  },
  {
    "type": "Keyboard Control",
    "name": "Keyboard Car Control"
  },
  {
    "type": "Clock",
    "name": "Simulation Clock",
    "params": {
      "Topic": "/lgsvl/clock"
    }
  }
]
```
