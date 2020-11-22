# Running latest Apollo with LGSVL Simulator [](#top)

This instruction is tested after [the last commit](https://github.com/ApolloAuto/apollo/commit/eac672b11ae20272f6d91f8eb119bfb942364ef5) enabling LGSVL Simulator with latest Apollo master. Commits after that are assumed to work as well, but not guaranteed. 

Big changes have recently been introduced in Apollo master; camera perception may not be working yet, and LiDAR perception is recently able to build but still unstable (and uses a lot of GPU memory making it challenging for Apollo to share an 8GB GPU with the LGSVL Simulator).

**For those who used our fork of Apollo 5.0 before: please note the new step to select the correct setup mode in Dreamview**.

[![](images/apollo-sim.png)](images/full_size_images/apollo-sim.png)

<h2> Table of Contents</h2>
[TOC]

## Getting Started [[top]] {: #getting-started data-toc-label='Getting Started'}
The guide outlines the steps required to setup Apollo for use with the LGSVL Simulator. If you have not already set up the simulator, please do so first by following the instructions [here](https://github.com/lgsvl/simulator).

## Prerequisites [[top]] {: #prerequisites data-toc-label='Prerequisites'}
* Ubuntu 18.04 or later
* Nvidia graphics card (required for Perception)
    - Nvidia proprietary driver (\>=440.33.01) must be installed
* Docker-CE 19.03 or later
* `nvidia-container-runtime`
* `libvulkan1`

## Setup [[top]] {: #setup data-toc-label='Setup'}

### Apollo [[top]] {: #docker data-toc-label='Apollo'}
To install and setup Apollo and its dependencies, follow instructions [here](https://github.com/ApolloAuto/apollo/blob/master/docs/quickstart/apollo_software_installation_guide.md).

This is the checklist for Apollo installation:

- ✅ Installed Nvidia GPU driver
- ✅ Installed Docker & post-installation steps
- ✅ Installed `nvidia-container-toolkit` or `nvidia-container-runtime`
- ✅ Cloned Apollo master branch
- ✅ Able to start Apollo docker container `bash docker/scripts/dev_start.sh`
- ✅ Able to enter docker container by `bash docker/scripts/dev_start.sh`
- ✅ Able to build Apollo in optimized GPU mode in Apollo container: `bash apollo.sh build_opt_gpu`

**NOTE**
The Apollo build may fail on machines with less than 1GB of RAM per CPU core due to aggressive parallelization in the build, as discussed in [Apollo issue 7719](https://github.com/ApolloAuto/apollo/issues/7719).

If the build fails, either re-start it until it succeeds, but if it continues to fail (especially when running the linker) then you'll need to address the low memory situation by either adding more memory to your build machine or enabling or [increasing available swap space](https://bogdancornianu.com/change-swap-size-in-ubuntu/). If your Apollo build is crashing on a 16GB machine with little or no swap, try setting it to 16GB.

## Launching Apollo alongside the Simulator [[top]] {: #launching-apollo-alongide-the-simulator data-toc-label='Launching Apollo alongisde the Simulator'}

[![](images/xe-simulator.png)](images/full_size_images/xe-simulator.png)

Here we describe only a simple case of driving from point A to point B using Apollo and the simulator. 

To launch apollo: 

* Launch and enter Apollo container using `dev_start.sh` and `dev_into.sh`.

* Start Apollo Dreamview (inside docker container): 

    Note: you may receive errors about dreamview not being build if you do not run the script from the `/apollo` directory.

```bash
./scripts/bootstrap_lgsvl.sh
```

* Launch bridge (inside docker container):

```bash
./scripts/bridge.sh
```

* Run the LG SVL Simulator outside of docker. See instructions in the [simulator repository](https://github.com/lgsvl/simulator)
    - Create a Simulation the `BorregasAve` map and `Lincoln2017MKZ (Apollo 5.0)` vehicle
    - Enter `localhost:9090` as the Bridge Connection String
    - (Optional) Enable Traffic and Pedestrians
    - (Optional) Set the Time of Day and weather settings
    - Submit the Simulation
    - Select the created Simulation and click "Play"

[![](images/apollo-master.png)](images/full_size_images/apollo-master.png)


* Open Apollo Dreamview in a browser by navigating to: `localhost:8888`
    - **NEW for Apollo Master**: Select the `Mkz Lgsvl` setup mode, from the menu to the left of the vehicle menu.
    - Select the `Lincoln2017MKZ LGSVL` vehicle and `Borregas Ave` map in the top right corner.
    - Open the **Module Controller** tap (on the left bar).
    - Enable **Localization**, **Transform**, **Perception**, **Traffic Light**, **Planning**, **Prediction**, **Routing**, and **Control**.
    - Navigate to the **Route Editing** tab.
    - Select a destination by clicking on a lane line and clicking **Submit Route**.
    - Watch the vehicle navigate to the destination.
- To stop the docker container run the `dev_start.sh stop` script in `apollo/docker/scripts` in a new terminal (not in the docker container).
  
* If you are using ufw, it is easiest to completely disable the firewall to allow connections `sudo ufw disable`
    - If that is not possible, add the following rules:
        - These are required even if running the simulator and Apollo on the same machine
        - `sudo ufw allow 8888`
        - `sudo ufw allow 9090`

### Adding a Vehicle <sub><sup>[top](#top)</sup></sub> {: #adding-a-vehicle data-toc-label='Adding a Vehicle'}
Only calibration files for Lincoln 2017 MKZ is included in the latest Apollo at this time. More calibration files map be added later.

### Adding an HD Map [[top]] {: #adding-an-hd-map data-toc-label='Adding an HD Map'}
Only HD map files for Borregas Ave is included in the the latest Apollo at this time. More HD maps may be added later. 

You can also download additional maps from [here](https://github.com/lgsvl/apollo-5.0/tree/simulator/modules/map/data), and manually add them to `/apollo/modules/map/data/`. 

Or you can export your own map from our [map annotation tool](https://www.lgsvlsimulator.com/docs/map-annotation/#export-map-annotations), name it `base_map.bin` and then generate `routing_map` and `sim_map` following [these instructions](https://github.com/ApolloAuto/apollo/tree/master/modules/map/data#difference-between-base_map-routing_map-and-sim_map).
