# <a name="top"></a> Simulator Messages

This page details the messages available by default in the simulator. Each bridge type (ROS, ROS Apollo, ROS2, CyberRT) will have its own message types.

<h2> Table of Contents</h2>
[TOC]

### ROS Message Types [[top]] {: #ros-message-types data-toc-label='ROS Message Types'}

The simulator supports many of the common standard ROS messages. Additionally, the simulator supports custom ROS messages defined for Autoware AI as well as the simulator's template messages for Autonomous Driving which are included in lgsvl_msgs.

Below is a list of these messages.

| Message Type | Namespace | Associated Sensor | Default Topic Name | Notes |
|:-|:-|:-|:-|:-|
| `std_msgs/Header` | Ros | - | - | Standard header used for ROS messages |
| `std_msgs/Time` | Ros | - | - | Usually used for timestamps in `std_msgs/Header` |
| `rosgraph_msg/Clock` | Ros | Clock Sensor | `/clock` | Required when `/use_sim_time` param is set to `true` |
| `std_msgs/ColorRGBA` | Ros | - | - | - |
| `sensor_msgs/CompressedImage` | Ros | Camera Sensor | - | JPEG encoded images - usually subscribed to by AD stacks for camera images |
| `sensor_msgs/Image` | Ros | Camera Sensor | - | Uncompressed images - usually subscribed to by AD stacks for camera images |
| `sensor_msgs/Imu` | Ros | Imu Sensor | - | Linear acceleration, angular velocity, and (optional) orientation as a quaternion |
| `sensor_msgs/Joy` | Ros | - | - | Reports the state of a joystick / wheel axes and buttons |
| `sensor_msgs/LaserScan` | Ros | - | - | Single beam lidar points reported as an array of range measurements |
| `sensor_msgs/PointCloud2` | Ros | Lidar Sensor | - | Point cloud data as typically output from a 3D lidar's driver |
| `sensor_msgs/PointField` | Ros | - | - | Included in `sensor_msgs/PointCloud2` to provide type information on the point |
| `sensor_msgs/NavSatStatus` | Ros | GPS Sensor | - | Status report for GNSS |
| `sensor_msgs/NavSatFix` | Ros | GPS Sensor | - | GNSS position fix reported llh format |
| `nmea_msgs/Sentence` | Ros | GPS Sensor | - | GNSS fix represented as an NMEA sentence - used in Autoware AI |
| `geometry_msgs/Point` | Ros | - | - | XYZ coordinates |
| `geometry_msgs/Pose` | Ros | - | - | 3D position and orientation (quaternion) |
| `geometry_msgs/PoseWithCovariance` | Ros | - | - | `geometry_msgs/Pose` with an additional covariance field |
| `geometry_msgs/Quaternion` | Ros | - | - | - |
| `geometry_msgs/Twist` | Ros | - | - | Linear and angular velocity |
| `geometry_msgs/TwistStamped` | Ros | - | `lgsvl_msgs/DetectedRadarObject`- | Contains s `geometry_msgs/Twist` and a `std_msgs/Header` |
| `geometry_msgs/TwistWithCovariance` | Ros | - | - | Contains a `geometry_msgs/Twist` with an additional covariance field |
| `geometry_msgs/Vector3` | Ros | - | - | 3D vector with x, y, z coordinates |
| `std_srvs/Empty` | Ros | - | - | Empty service |
| `std_srvs/SetBool` | Ros | - | - | Service for setting a boolean parameter |
| `std_srvs/Trigger` | Ros | - | - | Service for triggering an event |
| `autoware_msgs/steer_cmd` | Autoware | - | - | Steer command message |
| `autoware_msgs/accel_cmd` | Autoware | - | - | Acceleration command message |
| `autoware_msgs/brake_cmd` | Autoware | - | - | Brake command message |
| `autoware_msgs/lamp_cmd` | Autoware | - | - | Message for controlling lights on vehicle |
| `autoware_msgs/ControlCommand` | Autoware | - | - | Vehicle control command providing linear velocity, linear acceleration, and a steering angle |
| `autoware_msgs/VehicleCmd` | Autoware | Vehicle Control Sensor | `/vehicle_cmd` | Complete vehicle control command containing the other 'command' message - simulator subscribes to this |
| `autoware_msgs/DetectedObject` | Autoware | - | - | Attributes of objects detected by perception |
| `autoware_msgs/DetectedObjectArray` | Autoware | - | - | Array of `autoware_msgs/DetectedObject` |
| `lgsvl_msgs/BoundingBox2D`| Lgsvl | - | - | 2D ground truth bounding box for objects in view of the vehicle |
| `lgsvl_msgs/BoundingBox3D` | Lgsvl | - | - | 3D ground truth bounding box for objects in view of the vehicle |
| `lgsvl_msgs/Detection2D` | Lgsvl | - | - | Ground truth detection data for obstacles in 2D |
| `lgsvl_msgs/Detection2DArray` | Lgsvl | Ground Truth 2D Sensor | - | Array of ground truth values for 2D detections |
| `lgsvl_msgs/Detection3D` | Lgsvl | - | - | Ground truth detection data for obstacles in 3D |
| `lgsvl_msgs/Detection3DArray` | Lgsvl | Ground Truth 3D Sensor | - | Array of ground truth values for 3D detections |
| `lgsvl_msgs/Signal` | Lgsvl | - | - | Ground truth traffic light information |
| `lgsvl_msgs/SignalArray` | Lgsvl | Signal Sensor | - | Array of `lgsvl_msgs/Signal` for all traffic lights in view of ego |
| `lgsvl_msgs/CanBusData` | Lgsvl | CanBus Sensor | - | Collection of information available through CAN bus and published by simulator |
| `lgsvl_msgs/VehicleControlData` | Lgsvl | Vehicle Control Sensor | - | Vehicle control command simulator can subscribe to |
| 'lgsvl_msgs/VehicleStateData` | Lgsvl | Vehicle State Sensor | - | Feedback from vehicle control command published by simulator which can be used for implementing a feedback controller |
| `lgsvl_msgs/DetectedRadarObject` | Lgsvl | - | - | Representation of an obstacle detected by radar |
| `lgsvl_msgs/DetectedRadarObjectArray` | Lgsvl | Radar Sensor | - | Array of `lgsvl_msgs/DetectedRadarObject` published by the simulator |
| `lgsvl_srvs/Int` | Lgsvl | - | - | Service that sends an integer value |
| `lgsvl_srvs/String` | Lgsvl | - | - | Service that sends a string |


### ROS Apollo Message Types [[top]] {: #ros-apollo-message-types data-toc-label='ROS Apollo Message Types'}

Older versions of Apollo (<3.5) used a modified version of ROS which included support for messages serialized as protocol buffers. To use these messages, our [fork of rosbridge suite](https://github.com/lgsvl/rosbridge_suite) must be used alongside the simulator.

The supported messages are as follows. The Apollo ROS bridge supports all ROS messages in addition to the messages listed below.

| Message Type | Namespace | Associated Sensor | Default Topic Name | Notes |
|:-|:-|:-|:-|:-|
| `pb_msgs/Header` | Ros.Apollo | - | - | Default message header |
| `pb_msgs/Point3D` | Ros.Apollo | - | - | Point with x, y, z coordinates |
| `pb_msgs/GnssBestPose` | Ros.Apollo | GPS Sensor | - | Pose estimate from GNSS |
| `pb_msgs/Quaternion` | Ros.Apollo | - | - | Quaterntion |
| `pb_msgs/PointENU` | Ros.Apollo | - | - | East/North/Up representation of a point |
| `pb_msgs/Pose` | Ros.Apollo | - | - | Pose message including orientation, velocity, and acceleration |
| `pb_msgs/Gps` | Ros.Apollo | GPS Odometry Sensor | `/apollo/sensor/gnss/odometry` | GNSS Pose with header |
| `pb_msgs/Imu` | Ros.Apollo | IMU Sensor | `/apollo/sensor/gnss/corrected_imu` | IMU sensor data - linear acceleration and angular velocity |
| `pb_msgs/CorrectedImu` | Ros.Apollo | IMU Sensor | - | Pose with header |
| `pb_msgs/ControlCommand` | Ros.Apollo | Vehicle Control Sensor | `/apollo/control` | Vehicle control command subscribed to by the simulator |
|`pb_msgs/ADCTrajectory` | Ros.Apollo | - | `/apollo/planning` | Planning trajectory messsage from Apollo |
| `pb_msgs/Chassis` | Ros.Apollo | CanBus Sensor | `/apollo/canbus/chassis` | CAN Bus message published by simulator |
| `pb_msgs/ContiRadar` | Ros.Apollo | Radar Sensor | | Continental Radar output message |
| `pb_msgs/PerceptionObstacle` | Ros.Apollo | - | - | Obstacle detected by perception |
| `pb_msgs/PerceptionObstacles` | Ros.Apollo | - | `/apollo/perception/obstacles` | Array of `PerceptionObstacle`s |
| `pb_msgs/TrafficLight` | Ros.Apollo | - | - | Perceived state of a traffic light |
| `pb_msgs/TrafficLightDetection` | Ros.Apollo | - | `/apollo/perception/traffic_light` | State of all detected traffic lights |
| `pb_msgs/RoutingRequest` | Ros.Apollo | - | - | Message for sending a request for a route to a specified destination |
| `pb_msgs/RoutingResponse` | Ros.Apollo | - | - | Feedback received from a route setting request |

### ROS 2 Message Types [[top]] {: #ros2-message-types data-toc-label='ROS 2 Message Types'}

There is a lot of overlap between the message types supported for ROS and ROS 2 with the main difference being Autoware AI.

| Message Type | Namespace | Associated Sensor | Default Topic Name | Notes |
|:-|:-|:-|:-|:-|
| `std_msgs/Header` | Ros2 | - | - | Standard header used for ROS 2 messages |
| `builtin_interfaces/Time` | Ros2 | - | - | Usually used for timestamps in `std_msgs/Header` |
| `rosgraph_msg/Clock` | Ros2 | Clock Sensor | `/clock` | Required when `/use_sim_time` param is set to `true` |
| `std_msgs/ColorRGBA` | Ros2 | - | - | - |
| `sensor_msgs/CompressedImage` | Ros2 | Camera Sensor | - | JPEG encoded images - usually subscribed to by AD stacks for camera images |
| `sensor_msgs/Image` | Ros2 | Camera Sensor | - | Uncompressed images - usually subscribed to by AD stacks for camera images |
| `sensor_msgs/Imu` | Ros2 | Imu Sensor | - | Linear acceleration, angular velocity, and (optional) orientation as a quaternion |
| `nav_msgs/Odometry` | Ros2 | GPS Odometry Sensor | - | An odometry message containing a pose and a twist message |
| `sensor_msgs/LaserScan` | Ros2 | - | - | Single beam lidar points reported as an array of range measurements |
| `sensor_msgs/PointCloud2` | Ros2 | Lidar Sensor | - | Point cloud data as typically output from a 3D lidar's driver |
| `sensor_msgs/PointField` | Ros2 | - | - | Included in `sensor_msgs/PointCloud2` to provide type information on the point |
| `sensor_msgs/NavSatStatus` | Ros2 | GPS Sensor | - | Status report for GNSS |
| `sensor_msgs/NavSatFix` | Ros2 | GPS Sensor | - | GNSS position fix reported llh format |
| `nmea_msgs/Sentence` | Ros2 | GPS Sensor | - | GNSS fix represented as an NMEA sentence |
| `geometry_msgs/Point` | Ros2 | - | - | XYZ coordinates |
| `geometry_msgs/Pose` | Ros2 | - | - | 3D position and orientation (quaternion) |
| `geometry_msgs/PoseWithCovariance` | Ros2 | - | - | `geometry_msgs/Pose` with an additional covariance field |
| `geometry_msgs/Quaternion` | Ros2 | - | - | - |
| `geometry_msgs/Twist` | Ros2 | - | - | Linear and angular velocity |
| `geometry_msgs/TwistStamped` | Ros2 | - | `lgsvl_msgs/DetectedRadarObject`- | Contains s `geometry_msgs/Twist` and a `std_msgs/Header` |
| `geometry_msgs/TwistWithCovariance` | Ros2 | - | - | Contains a `geometry_msgs/Twist` with an additional covariance field |
| `geometry_msgs/Vector3` | Ros2 | - | - | 3D vector with x, y, z coordinates |


### CyberRT Message Types [[top]] {: #cyberrt-message-types data-toc-label='CyberRT Message Types'}

Latest versions of Apollo (>=3.5) use a middleware called CyberRT. The LGSVL Simulator provides a bridge for communication with Apollo using CyberRT messages. The supported messages are listed below:

| Message Type | Namespace | Associated Sensor | Default Channel Name | Notes |
|:-|:-|:-|:-|:-|
| `apollo.canbus.Chassis` | Cyber | CanBus Sensor | `/apollo/canbus/chassis` | CAN bus message published by the simulator |
| `apollo.control.ControlCommand` | Cyber | CanBus Sensor | `/apollo/control` | Vehicle control message the simulator subscribes to |
| `apollo.localization.Gps` | Cyber | GPS Odometry Sensor | `/apollo/sensor/gnss/odometry` | Vehicle world coordinates in UTM as well as orientation and velocity |
| `apollo.drivers.gnss.GnssBestPose` | Cyber | GPS Sensor | `/apollo/sensor/gnss/best_pose` | World coordinates in LLH |
| `apollo.drivers.gnss.Imu` | Cyber | IMU Sensor | `/apollo/sensor/gnss/imu` | Raw IMU sensor measurements |
| `apollo.drivers.gnss.CorrectedImu` | Cyber | IMU Sensor | `/apollo/sensor/gnss/corrected_imu` | Corrected IMU measurements along with orientation |
| `apollo.drivers.gnss.InsStat` | Cyber | GPS-INS Status | `/apollo/sensor/gnss/ins_stat` | INS Status |
| `apollo.drivers.ContiRadar` | Cyber | Radar Sensor | `/apollo/sensor/conti_radar` | Continental radar messages |
| `apollo.drivers.PointCloud` | Cyber | Lidar Sensor | `/apollo/sensor/lidar128/compensator/PointCloud2` | Compensated lidar pointcloud message |
| `apollo.drivers.Image` | Cyber | Camera Sensor | `/apollo/sensor/camera/front_12mm/image` | images from RGB camera |
| `apollo.drivers.CompressedImage` | Cyber | Camera Sensor | `/apollo/sensor/camera/front_12mm/image/compressed` | Compressed (jpg encoded) images from RGB camera |

