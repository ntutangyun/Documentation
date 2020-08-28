# Autoware.AI 1.14.0 with LGSVL Simulator [](#top)

**The software and source code in this repository are intended only for use with the LGSVL simulator and *should not* be used in a real vehicle.**

<h2>Table of Contents</h2>
[TOC]

## General [[top]] {: #general data-toc-label='General'}

<div class="video-container">
    <iframe style="display:block;margin:auto;" width="696" height="391" src="https://www.youtube.com/embed/C4ngQWUseJI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
<br>

This guide goes through how to run Autoware.AI with the LGSVL simulator.

In order to run Autoware with the LGSVL simulator, it is easiest to pull the official Autoware Docker image (see the [official guide, Case 1](https://github.com/Autoware-AI/autoware.ai/wiki/Generic-x86-Docker#run-an-autoware-docker-container) for more details), but it is also possible to [build Autoware from source](https://github.com/Autoware-AI/autoware.ai/wiki/Source-Build).

Autoware communicates with the LGSVL simulator using the rosbridge_suite, which provides JSON interfacing with ROS publishers/subscribers. The official Autoware Docker containers have rosbridge_suite included.

## Setup [[top]] {: #setup data-toc-label='Setup'}

### Requirements [[top]] {: #requirements data-toc-label='Requirements'}

- Linux operating system
- NVIDIA graphics card

### Install Docker CE [[top]] {: #install-docker-ce data-toc-label='Install Docker CE'}

To install Docker CE please refer to the [official documentation](https://docs.docker.com/install/linux/docker-ce/ubuntu/). We also suggest following through with the [post installation steps](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) to run docker as a non-root user.

### Install NVIDIA Container Toolkit [[top]] {: #install-nvidia-container-toolkit data-toc-label='Install NVIDIA Container Toolkit'}

Before installing the NVIDIA Container Toolkit (nvidia-docker), make sure that you have an appropriate NVIDIA driver installed. To test if the NVIDIA drivers are properly installed enter `nvidia-smi` in a terminal. If the drivers are installed properly an output similar to the following should appear:

```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 440.59       Driver Version: 440.59       CUDA Version: 10.2     |
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

Install the NVIDIA Container Toolkit by following the instructions [here](https://github.com/NVIDIA/nvidia-docker#quickstart).

### Install LGSVL Simulator [[top]] {: #install-lgsvl-simulator data-toc-label='Install LGSVL Simulator'}

Follow the instructions [here](https://github.com/lgsvl/simulator).

### Install Autoware [[top]] {: #install-autoware data-toc-label='Install Autoware'}

Make sure you have [Git Large File Storage](https://git-lfs.github.com/) (LFS) installed **before** cloning the repository in the next step. If `git lfs` outputs `git: 'lfs' is not a git command.`, then you need to install it:

- Instructions for installation are [here](https://help.github.com/en/articles/installing-git-large-file-storage).

- Verify the installation:

        $ git lfs install
        Git LFS initialized.

Create a directory called `shared_dir` in your home directory to hold HD maps and launch files for the simulator. The Autoware Docker container will mount this folder.
```bash
mkdir ~/shared_dir
cd ~/shared_dir
git clone https://github.com/lgsvl/autoware-data.git
```

If there wasn't a line beginning with `Filtering content:` output, then Git LFS hasn't been installed. Remove the `autoware-data` directory, install Git LFS with `git lfs install`, and then re-issue the `git clone`.

Clone the `docker` repository from `autoware.ai` into a working directory:

```bash
cd $WORKING_DIRECTORY
git clone https://github.com/Autoware-AI/docker.git
```

## Launch Autoware Alongside LGSVL Simulator [[top]] {: #launch-autoware-alongside-lgsvl-simulator data-toc-label='Launch Autoware Alongside LGSVL Simulator'}

Run the Autoware 1.14.0 container and enter into it:

```bash
cd $WORKING_DIRECTORY/docker/generic
./run.sh -t 1.14.0
```
If you get the usermod error as follows:
```
usermod: user autoware is currently used by process 1
```
Check if the $UID is 1000
```
$ echo $UID
```
If your $UID is 1000, you would not have usermod error. Otherwise, it's better to build container locally to avoid usermod error as follows:
```
$ ./build.sh --version 1.14.0
$ ./run.sh -t local
```

Once inside the container, install a missing ROS package:

```bash
sudo apt update && sudo apt install ros-$ROS_DISTRO-image-transport-plugins -y
```
If you need to check which $ROS_DISTRO you have installed run the following:

```bash
ls /opt/ros/
```

Launch the runtime manager:

```bash
roslaunch runtime_manager runtime_manager.launch
```

A few terminals will open, as well as a GUI for the runtime manager. In the runtime manager, click on the 'Quick Start' tab and load the following launch files from `~/shared_dir/autoware-data/BorregasAve/` by clicking "Ref" to the right of each text box:

- `my_map.launch`
- `my_sensing_simulator.launch`
- `my_localization.launch`
- `my_detection.launch`
- `my_mission_planning.launch`
- `my_motion_planning.launch`

[![](images/autoware-runtime-manager.png)](images/autoware-runtime-manager.png)

Click "Map" to load the launch file pertaining to the HD maps. An "Ok" should appear to the right of the "Ref" button when successfully loaded. Then click "Sensing" which also launches rosbridge.

- Run the LGSVL simulator
- Create a Simulation choosing `BorregasAve` map and `Jaguar2015XE (Autoware)` or another Autoware compatible vehicle.
- Enter `localhost:9090` for the Bridge Connection String.
- Run the created Simulation

A vehicle should appear in Borregas Ave in Sunnyvale, CA.

In the Autoware Runtime Manager, continue loading the other launch files - click "Localization" and wait for the time to display to the right of "Ref".

Then click "Rviz" to launch Rviz - the vector map and location of the vehicle in the map should show.

The vehicle may be mis-localized as the initial pose is important for NDT matching. To fix this, click "2D Pose Estimate" in Rviz, then click an approximate position for the vehicle on the map and drag in the direction it is facing before releasing the mouse button. This should allow NDT matching to find the vehicle pose (it may take a few tries). Note that the point cloud will not show up in rviz until ndt matching starts publishing a pose.

[![](images/autoware-ndt-matching.png)](images/autoware-ndt-matching.png)


An alternative would be to use GNSS for an initial pose or for localization. Open my_localization.launch file in map folder of autoware-data repository and uncomment nmea2tfpose part and comment ndt_matching instead.

### Driving by following vector map:
To drive following the HD map follow these steps:
- in rviz, mark a destination by clicking '2D Nav Goal' and clicking at the destination and dragging along the road direction. Make sure to only choose a route that looks valid along the lane centerlines that are marked with orange lines in rviz. If an invalid destination is selected nothing will change in rviz, and you will need to relaunch the `Mission Planning` and `Motion Planning` launch files in the `Quick Launch` tab to try another destination.
After choosing a valid destination the route will be highlighted in blue in rviz.

[![](images/autoware-valid-route.png)](images/autoware-valid-route.png)

In Mission Planning launch file, waypoint_planner runs as global planner, dp_planner does as local planner. In Motion Planning launch file, pure_pursuit runs as controller.

### Adding a Vehicle [[top]] {: #adding-a-vehicle data-toc-label='Adding a Vehicle'}
The default vehicles have the calibration files included in the [LGSVL Autoware Data](https://github.com/lgsvl/autoware-data) Github repository.

### Adding an HD Map [[top]] {: #adding-an-hd-map data-toc-label='Adding an HD Map'}
The default maps have the Vector map files included in the [LGSVL Autoware Data](https://github.com/lgsvl/autoware-data) Github repository.


