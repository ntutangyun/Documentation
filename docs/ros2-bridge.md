# LGSVL Simulator ROS 2 Bridge

The LGSVL Simulator can publish and subscribe to ROS 2 messages by connecting to the [ROS2 LGSVL Bridge](https://github.com/lgsvl/ros2-lgsvl-bridge). This custom, native ROS2 bridge yields higher performance than the previously used [ros2-web-bridge](https://github.com/RobotWebTools/ros2-web-bridge).

This document describes the installation process.

## Installing Dependencies
The ROS2 LGSVL Bridge requires the following packages to be built:
- ROS2 (rcutils and rcl)
- colcon
- boost

### Installing ROS 2
See the [official ROS 2 installation guide](https://index.ros.org/doc/ros2/Installation/#installationguide) to install ROS 2. The choice of the distribution will somewhat depend on the Ubuntu version installed. Ubuntu 18.04 supports Dashing Diademata (`dashing`) and Eloquent Elusor (`eloquent`), with `dashing` being the LTS distro. Ubuntu 20.04 only supports Foxy Fitzroy (`foxy`), which is also an LTS distro.

The ROS 2 LGSVL Bridge supports Dashing Diademata and newer distributions. For Ubuntu versions older than 18.04, which would require older ROS 2 distributions, we suggest using [ROS 2 docker images](https://hub.docker.com/_/ros).

### Installing Colcon
Colcon is a command line tool that facilitates building ROS packages. To install:
```bash
sudo apt update
sudo apt install python3-colcon-common-extensions
```

### Installing Boost
To install boost run:
```bash
sudo apt install libboost-all-dev
```

## Installing the ROS 2 LGSVL Bridge
Follow these steps to install the ROS2 LGSVL Bridge:
- Clone the ros2-lgsvl-bridge repository from github:
```bash
    git clone https://github.com/lgsvl/ros2-lgsvl-bridge.git
```
- Source the installed ROS2 distribution in the terminal window:
```bash
    source /opt/ros/(name\of\ros2\distro)/setup.bash
```
- Switch to the correct devel branch for the installed ROS 2 distribution:
```bash
    cd ros2-lgsvl-bridge
    git checkout ${ROS_DISTRO}-devel
```
- Build using colcon:
```bash
    colcon build --cmake-args '-DCMAKE_BUILD_TYPE=Release'
```

## Running the ROS2 LGSVL Bridge
If the Bridge is already built, to run ROS2 bridge:
```bash
    source (path\to\bridge\repository)/install/setup.bash
    lgsvl_bridge
```
