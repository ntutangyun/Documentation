# Running latest Apollo with LGSVL Simulator [](#top)

This instruction is tested after [the last commit](https://github.com/ApolloAuto/apollo/commit/eac672b11ae20272f6d91f8eb119bfb942364ef5) enabling LGSVL Simulator with latest Apollo master. Commits after that are assumed to work as well, but not guaranteed.

[![](images/apollo-sim.png)](images/full_size_images/apollo-sim.png)

<h2> Table of Contents</h2>
[TOC]

## Getting Started [[top]] {: #getting-started data-toc-label='Getting Started'}
The guide outlines the steps required to setup Apollo for use with the LGSVL Simulator. If you have not already set up the simulator, please do so first by following the instructions [here](https://github.com/lgsvl/simulator).

## Prerequisites [[top]] {: #prerequisites data-toc-label='Prerequisites'}
* Ubuntu 16.04 or later (Ubuntu 18.04 is preferred)
* Nvidia graphics card (required for Perception)
    - Nvidia proprietary driver (\>=410.48) must be installed

## Setup [[top]] {: #setup data-toc-label='Setup'}

### Docker [[top]] {: #docker data-toc-label='Docker'}
Apollo is designed to run out of docker containers. The image will mount this repository as a volume so the image will not need to be rebuilt each time a modification is made.

#### Installing Docker CE [[top]] {: #installing-docker-ce data-toc-label='Installing Docker CE'}
To install Docker CE please refer to the [official documentation](https://docs.docker.com/install/linux/docker-ce/ubuntu/).

**NOTE** 
Apollo does not work if the docker is started with `sudo`.
We suggest following through with the [post installation steps](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user).


#### Installing Nvidia Docker [[top]] {: #installing-nvidia-docker data-toc-label='Installing Nvidia Docker'}
Before installing nvidia-docker make sure that you have an appropriate Nvidia driver installed.
To test if nvidia drivers are properly installed enter `nvidia-smi` in a terminal. If the drivers are installed properly an output similar to the following should appear.

```
	+-----------------------------------------------------------------------------+
    | NVIDIA-SMI 440.64       Driver Version: 440.64       CUDA Version: 10.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  GeForce GTX 1080    Off  | 00000000:01:00.0  On |                  N/A |
    | 27%   29C    P8     7W / 180W |    579MiB /  8117MiB |      1%      Default |
    +-------------------------------+----------------------+----------------------+
                                                                                   
    +-----------------------------------------------------------------------------+    
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |    0      1745      G   /usr/lib/xorg/Xorg                            40MiB |
    |    0      1862      G   /usr/bin/gnome-shell                          49MiB |
    |    0      4409      G   /usr/lib/xorg/Xorg                           223MiB |
    |    0      4545      G   /usr/bin/gnome-shell                         140MiB |
    |    0      4962      G   ...uest-channel-token=10798087356903621100    27MiB |
    |    0      9570      G   /proc/self/exe                                50MiB |
    |    0     17619      G   ...uest-channel-token=14399957398263092148    40MiB |
    +-----------------------------------------------------------------------------+
```

The installation steps for nvidia-docker are available at the [official repo](https://github.com/NVIDIA/nvidia-docker). 

### Cloning the Repository [[top]] {: #cloning-the-repository data-toc-label='Cloning the Repository'}
Clone latest Apollo using the following command:

```bash
git clone https://github.com/ApolloAuto/apollo
```

### Building Apollo and bridge [[top]] {: #building-apollo-and-bridge data-toc-label='Building Apollo and bridge'}
Now everything should be in place to build Apollo. Apollo must be built from the container. To launch the container navigate to the directory where the repository was cloned and enter:

```bash
./docker/scripts/dev_start.sh
```

This should launch the container and mount a few volumes. It could take a few minutes to pull the latest volumes on the first run.

To get into the container:

```bash
./docker/scripts/dev_into.sh
```

Build Apollo:

```bash
./apollo.sh build_gpu
```

**NOTE** 
Apollo build may fail on machines with (for example) 16GB or less of RAM, depending on the number of cores due to aggressive parallelization in the build as discussed in [Apollo issue 7719](https://github.com/ApolloAuto/apollo/issues/7719).

If the build fails, either re-start it until it succeeds, or try changing line 141 of `function build()` in `apollo.sh` as follows:

Replace this:
   `JOB_ARG="--jobs=$(nproc) --ram_utilization_factor 80"`

With this, which will use all but two cores for the build:
   `JOB_ARG="--jobs=$(expr $(nproc) - 2 ) --ram_utilization_factor 70"`

## Launching Apollo alongside the Simulator [[top]] {: #launching-apollo-alongide-the-simulator data-toc-label='Launching Apollo alongisde the Simulator'}

[![](images/xe-simulator.png)](images/full_size_images/xe-simulator.png)

Here we only describe only a simple case of driving from point A to point B using Apollo and the simulator. 

To launch apollo, first launch and enter a container as described in the previous steps.

* To start Apollo:

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
    - NEW for Apollo Master: Select the `Mkz Lgsvl` setup mode, from the menu to the left of the vehicle menu.
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


##  Copyright and License [[top]] {: #copyright-and-license data-toc-label='Copyright and License'}

Copyright (c) 2020 LG Electronics, Inc.

This software contains code licensed as described in LICENSE.
