# <a name="top"></a>lgsvl_msgs
`lgsvl_msgs` is a ROS / ROS2 hybrid package that provides AD stack agnostic message definitions for interfacing with the LGSVL Simualtor.

The package contains the following definitions:
```
  - Detection3DArray.msg    # A list of 3D detections
  - Detection3D.msg         # 3D detection including id, label, score, and 3D bounding box
  - BoundingBox3D.msg       # A 3D bounding box definition
  - Detection2DArray.msg    # A list of 2D detections
  - Detection2D.msg         # 2D detection including id, label, score, and 2D bounding box
  - BoundingBox2D.msg       # A 2D bounding box definition
  - SignalArray.msg         # A list of traffic light detections
  - Signal.msg              # 3D detection of a traffic light including id, label, score, and 3D bounding box
  - CanBusData.msg          # Can bus data for an ego vehicle published by the simulator
  - VehicleControlData.msg  # Defines vehicle control data the simulator subscribes to
  - VehicleStateData.msg    # Data published by the simulator describing the full state of an ego vehicle
```

# Installation
`lgsvl_msgs` is built by the ROS build farm. Binaries can be installed on Ubuntu as follows:
```bash
sudo apt update
sudo apt install ros-$ROS_DISTRO-lgsvl_msgs
```

**Note:** It may take up to a week for the builds to become available after a release.

# Building from source
# ROS
- Create a catkin workspace for the package and clone the repository into the source folder:
```bash
    mkdir -p catkin_ws/src
    cd catkin_ws/src
    git clone https://github.com/lgsvl/lgsvl_msgs.git
```
- Build the package from workspace root:
```bash
    cd ..
    catkin_make
```
- Source in terminal where rosnodes who publish / subscribe to the messages are running:
```bash
    source devel/setup.bash
```

# ROS 2
- Clone repository and build with colcon:
```bash
    git clone https://github.com/lgsvl/lgsvl_msgs.git
    cd lgsvl_msgs
    colcon build
```

- Source in terminal where rosnodes who publish / subscribe to the messages are running:
```bash
    source install/setup.bash
```


