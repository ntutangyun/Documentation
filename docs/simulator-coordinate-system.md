# Understanding LGSVL Simulator Coordinate Systems [](#top)

<h2>Table of Contents</h2>
[TOC]

Inside the simulator positions and rotations are represented by [Transforms](https://docs.unity3d.com/560/Documentation/Manual/Transforms.html) in Unity. Unity uses a left-handed ZXY coordinate system for transforms with the Y-axis being the vertical axis. Each simulation object such as vehicles, sensors, maps, traffic lights, etc. will have a transform associated with it. Understanding the transforms is especially important for defining custom sensor configurations for ego vehicles.


### Converting between coordinate systems [[top]] {: #converting-between-coordinate-systems data-toc-label='Converting Between Coordinate Systems'}
The right-handed XYZ coordinate system is more commonly used in Robotics and Autonomous Vehicle applications and users may require to convert coordinate systems for some use-cases when using the LGSVL Simulator.

The tables below summarize conversions for 3D position vectors:

| ZXY (Unity) | XYZ (RH) |
|---|---|
| Z | X |
| X | -Y |
| Y | Z |

| XYZ (RH) | ZXY (Unity) |
|---|---|
| X | Z |
| Y | -X |
| Z | Y |

The tables below summarize conversions for 3D rotation vectors:

| ZXY (Unity) | XYZ (RH) |
|---|---|
| Z | -X |
| X | Y |
| Y | -Z |

| XYZ (RH) | ZXY (Unity) |
|---|---|
| X | -Z |
| Y | X |
| Z | -Y |


The tables below summarize conversions for quaternions:


| ZXY (Unity) | XYZ (RH) |
|---|---|
| Z | -X |
| X | Y |
| Y | -Z |
| W | W |


| XYZ (RH) | ZXY (Unity) |
|---|---|
| X | Y |
| Y | -Z |
| Z | -X |
| W | W |


### Map Origin [[top]] {: #map-origin data-toc-label='Map-Origin'}

Each simulator map includes a gameObject called `MapOrigin`. The Map Origin is the reference point of the map within Unity and has coordinate values of (0, 0, 0). The real-world position of the Map Origin is needed to output world coordinates using GNSS sensors. These values are stored within the gameObject as Easting and Northing values expressed in the [UTM coordinate system.](https://en.wikipedia.org/wiki/Universal_Transverse_Mercator_coordinate_system) as expressed below:

![](images/mapOrigin.png)

**Note:** To ensure proper heading values in GNSS related topics, the map must be oriented in such a way the the Z-axis of the unity world coordinate is pointing East. Otherwise all orientation values will have an offset.


### Vehicle Root Position (BaseLink) [[top]] {: #vehicle-root-position data-toc-label='Vehicle-Root-Position'}
The vehicle root is the reference point of the ego vehicle model defined in a GameObject called `BaseLink` under the ego vehicle. `BaseLink` is intended to be placed at the center of the rear axle, however, if the GameObject does not exist it will be created when the simulation starts and will be placed at the pivot of the ego vehicle model. All sensor positions entered into the sensor configuration in the web UI are defined in the coordinate frame attached to `BaseLink`.

#### Setting up BaseLink
1. Create a new GameObject named `BaseLink` and click 'Add Component' to add a script called `BaseLink`.
![](images/BaseLink1.png)
2. Move position to the center of the rear axle.
![](images/BaseLink2.png)
3. Add BaseLink.cs and link it to public variable in VehicleSMI.cs
![](images/BaseLink3.png)


### Transform Sensor [[top]] {: #transform-sensor data-toc-label='Transform-Sensor'}
Sometimes it can be useful to create a coordinate frame at some point on the ego vehicle to use as a reference for sensor positions. The [Transform Sensor](sensor-json-options.md#transform-sensor) provides this functionality. The Transform Sensor is an "empty" sensor, meaning that it has no sensor functionality and only has a coordinate frame and can be used as a parent for other sensors in the Simulator sensor configuration.


### Customizing sensor configurations [[top]] {: #customizing-sensor-configurations data-toc-label='Customizing-Sensor-Configurations'}
Customized sensor configurations are created in the LGSVL simulator Web UI by providing JSON formatted descriptions (covered [here](sensor-json-options.md)).