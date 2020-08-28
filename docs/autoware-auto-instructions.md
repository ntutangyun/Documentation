# Autoware.Auto with LGSVL Simulator [](#top)

<h2> Table of Contents</h2>
[TOC]

## Overview [[top]] {: #general data-toc-label='Overview'}

This guide describes setting up and using Autoware.Auto with the LGSVL simulator. As Autoware.Auto is still under-development, full self-driving is not yet possible. This guide will focus on running individual modules which have been implemented.

## Setup [[top]] {: #setup data-toc-label='Setup'}

### Requirements [[top]] {: #requirements data-toc-label='Requirements'}

- Linux operating system
- Nvidia graphics card

#### Installing Docker CE [[top]] {: #installing-docker-ce data-toc-label='Installing Docker CE'}

To install Docker CE please refer to the [official documentation](https://docs.docker.com/install/linux/docker-ce/ubuntu/). We also suggest following through with the [post installation steps](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) to run docker as a non-root user.

#### Installing Nvidia Docker [[top]] {: #installing-nvidia-docker data-toc-label='Installing Nvidia Docker'}

- Before installing nvidia-docker make sure that you have an appropriate Nvidia driver installed. To test if nvidia drivers are properly installed enter `nvidia-smi` in a terminal. If the drivers are installed properly an output similar to the following should appear.

```
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 390.87                 Driver Version: 390.87                    |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  GeForce GTX 108...  Off  | 00000000:65:00.0  On |                  N/A |
    |  0%   59C    P5    22W / 250W |   1490MiB / 11175MiB |      4%      Default |
    +-------------------------------+----------------------+----------------------+
                                                                                
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |    0      1187      G   /usr/lib/xorg/Xorg                           863MiB |
    |    0      3816      G   /usr/bin/gnome-shell                         305MiB |
    |    0      4161      G   ...-token=7171B24E50C2F2C595566F55F1E4D257    68MiB |
    |    0      4480      G   ...quest-channel-token=3330599186510203656   147MiB |
    |    0     17936      G   ...-token=5299D28BAAD9F3087B25687A764851BB   103MiB |
    +-----------------------------------------------------------------------------+
```

- Install [nvidia docker](https://github.com/NVIDIA/nvidia-docker).

    *Note:* For docker 19.03 and newer nvidia GPUs are natively supported as devices in docker runtime, and nvidia-docker2 is deprecated, however, because Autoware.auto uses the `--runtime nvidia` argument nvidia-docker2 will need to be installed even for newer docker versions.

### Installing Autoware.auto [[top]] {: #installing-autoware-auto data-toc-label='Installing Autoware.auto'}

- Follow the [installation and development setup](https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/installation.html) guide for Autoware.auto.

### Simulator installation [[top]] {: #simulator-installation data-toc-label='Simulator Installation'}

- Download and extract the latest [simulator release](https://github.com/lgsvl/simulator/releases/latest) under the ~/adehome folder.
- (Optional) Download the latest [PythonAPI release](https://github.com/lgsvl/PythonAPI/releases/latest) (make sure the release version matches the simulator) and install it using pip:

```bash
cd PythonAPI
pip3 install --user .
```
### Install ROS2 LGSVL Bridge [[top]] {: #install-ros2-lgsvl-bridge data-toc-label='Install ROS2 LGSVL Bridge'}
###### Downloading
```bash
cd adehome
git clone https://github.com/lgsvl/ros2-lgsvl-bridge.git
```
###### Building
Refer to README.md in the repo.
```bash
cd ros2-lgsvl-bridge
colcon build --cmake-args '-DCMAKE_BUILD_TYPE=Release'
```
###### Running {: #lgsvl-bridge-running}
Refer to README.md in the repo.
```bash
source ~/ros2-lgsvl-bridge/install/setup.bash
lgsvl_bridge
```

### Install ROS2 LGSVL Messages [[top]] {: #install-ros2-lgsvl-messages data-toc-label='Install ROS2 LGSVL Messages'}
###### Downloading
```bash
mkdir -p ~/adehome/AutowareAuto/src/external/lgsvl_msgs
cd ~/adehome/AutowareAuto/src/external/lgsvl_msg
git clone https://github.com/lgsvl/lgsvl_msgs.git
```
###### Building
```bash
# In the ade container
cd ~/AutowareAuto
colcon build --cmake-args '-DCMAKE_BUILD_TYPE=Release'
# You may want to build only lgsvl_msgs package with the following command.
colcon build --packages-select lgsvl_msgs --cmake-args '-DCMAKE_BUILD_TYPE=Release'
```
###### Testing
```bash
cd ~/AutowareAuto
source install/setup.bash
ros2 msg list |grep lgsvl_msgs
# If you can see the list of lgsvl_msgs, they're ready to be used.
```

## Run Simulator alongside Autoware.Auto [[top]] {: #run-simulator-alongside-autoware-auto data-toc-label='Run Simulator alongside Autoware.Auto'}
The ROS2 web bridge allows the simulator and Autoware.auto to communicate. To test this connection we can visualize sensor data from the simulator in rviz2 (running in the Autoware.auto container).

- Start the Autoware.Auto containers without NVIDIA setup:

```bash
cd ~/adehome/AutowareAuto
source .aderc
ade start
```

- Start the Autoware.Auto containers with NVIDIA setup:
- ~/adehome/AutowareAuto/.aderc-nvidia:

```bash
export ADE_DOCKER_RUN_ARGS="--cap-add=SYS_PTRACE --net=host --privileged --add-host ade:127.0.0.1 -e RMW_IMPLEMENTATION=rmw_cyclonedds_cpp --runtime=nvidia -ti --rm -e DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix \
-e NVIDIA_VISIBLE_DEVICES=all \
-e NVIDIA_DRIVER_CAPABILITIES=compute,utility,display"
export ADE_GITLAB=gitlab.com
export ADE_REGISTRY=registry.gitlab.com
export ADE_DISABLE_NVIDIA_DOCKER=false
export ADE_IMAGES="
  registry.gitlab.com/autowarefoundation/autoware.auto/autowareauto/ade:master
  registry.gitlab.com/autowarefoundation/autoware.auto/autowareauto:master
  nvidia/cuda
"
```

```bash
cd ~/adehome/AutowareAuto
source .aderc-nvidia
ade start
```

- Enter the container and start rviz2:

```bash
ade enter
cd ~/AutowareAuto
colcon build
source ~/AutowareAuto/install/setup.bash
rviz2 -d /home/"${USER}"/AutowareAuto/install/autoware_auto_examples/share/autoware_auto_examples/rviz2/autoware.rviz
```

- Start the LGSVL Simulator in ADE container by launching the executable and click on the button to open the web UI.

```bash
$ ~/AutowareAuto/lgsvl_simulator/simulator
```

- When you see Open Browser in a window, enter localhost:8080 in browser.

- In the Vehicles tab look for `AWF Lexus2016 RX Hybrid`. If not available download it from [here](https://content.lgsvlsimulator.com/vehicles/lexusrx2016/) and follow [these instructions](https://www.lgsvlsimulator.com/docs/vehicles-tab/#how-to-add-a-vehicle) to add it.
    - Click on the wrench icon for the Lexus vehicle:
    - Change the bridge type to `ROS2`
    - Use the following JSON configuration [Autoware Auto JSON Example](autoware-auto-json-example.md)

- Switch to the Simulations tab and click the `Add new` button:
    - Enter a name and switch to the `Map & Vehicles` tab
    - Select a map from the drop down menu. If none are available follow [this guide](https://www.lgsvlsimulator.com/docs/maps-tab/#where-to-find-maps) to get a map.
    - Select the `Lexus2016RXHybrid` from the drop down menu. In the bridge connection box to the right enter the bridge address (default: `localhost:9090`)
    - Click submit
    Select the simulation and press the play button in the bottom right corner of the screen

- Launch ROS2 LGSVL bridge in a new terminal:

**NOTE*** [ROS2 LGSVL Bridge](#lgsvl-bridge-running) needs to be running.

You should now be able to see the lidar point cloud in rviz (see image below).

If the pointcloud is not visible make sure the Fixed Frame (under Global Options) is set to `lidar_front` and that a PointCloud2 message is added which listens on the `/lidar_front/points_raw` topic.

![](images/autoware-auto-rviz.png)
