# Modular Testing

Our `3D Ground Truth sensor` and `Signal sensor` now publish ground truth perception data to Apollo stack via CyberRT bridge or ROS Apollo bridge. The modular testing feature is useful for testing planning module of Apollo stack based on the assumption that the perception output is 100% accurate without any errors. In other words, we can completely bypass Apollo's perception modules (i.e., object detection and traffic light detection) and use ground truth labels for perception published by our simulator instead.

<div class="video-container">
    <iframe style="display:block;margin:auto;" width="696" height="391" src="https://www.youtube.com/embed/Sr-OGw3aGKI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
<br>

## Sensor Specifications

### 3D Ground Truth sensor [(more details)](https://www.lgsvlsimulator.com/docs/sensor-json-options/#3d-ground-truth)

[![Ground Truth sensor visualization](./images/modular-testing/ground-truth-sensor.png)](./images/modular-testing/full_size_images/ground-truth-sensor.png)

`3D Ground Truth sensor` replaces Apollo's object detection module. It detects every NPC including vehicles and pedestrians around the EGO vehicle within a distance, which can be specified using a sensor parameter `MaxDistance`, and publishes ground truth labels such as 3D bounding boxes for the detected objects.

Major message fields:

- ID: integer ID for an object
- Timestamp: time in seconds since Unix epoch
- Velocity: object velocity vector in m/s
- Acceleration: object acceleration vector in m/s
- Width: object width in meters
- Length: object length in meters
- Height: object height in meters
- Type: vehicle or pedestrian
- Position: easting, northing, altitude in GPS position
- Theta: object heading in radians
- Tracking time: duration of detection in seconds
- Polygon point: corner points for an object

Sensor parameters:

- Frequency: 10
- MaxDistance: 100
- Topic: **/apollo/perception/obstacles**

### Signal sensor [(more details)](https://www.lgsvlsimulator.com/docs/sensor-json-options/#signal-sensor)

[![Signal sensor visualization](./images/modular-testing/signal-sensor.png)](./images/modular-testing/full_size_images/signal-sensor.png)

`Signal sensor` replaces Apollo's traffic light detection module. It detects every signal that is connected to the current lane of the EGO vehicle within a distance, which can also be specified using a sensor parameter `MaxDistance`, and then publishes the signal IDs as well as the current signal color information. It is important to note that the signal ID coming from the map annotation in the simulator must match with the ID in the Apollo HD map you're currently using in Apollo, because Apollo uses this ID to locate the detected traffic light in the map.

Major message fields:

- ID: traffic light string ID must match with the signal ID in Apollo HD map
- Timestamp: time in seconds since Unix epoch
- Color: red, yellow, green, or black
- Blink: is traffic light blinking

Sensor parameters:

- Frequency: 10
- MaxDistance: 100
- Topic: **/apollo/perception/traffic_light**


### Sensor JSON

The following sensor parameters can be added to a vehicle sensor configuration (and can replace the lidar and camera sensors which will improve simulation performance). Refer to [How to Change the Configuration of a Vehicle](vehicles-tab.md#how-to-change-the-configuration-of-a-vehicle) for more information on vehicle sensor configuration.

```JSON
  {
    "type": "3D Ground Truth",
    "name": "3D Ground Truth",
    "params": {
      "Frequency": 10,
      "MaxDistance": 100,
      "Topic": "/apollo/perception/obstacles"
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
    "type": "Signal",
    "name": "Signal Sensor",
    "params": {
      "Frequency": 10,
      "MaxDistance": 100,
      "Topic": "/apollo/perception/traffic_light"
    },
    "transform": {
      "x": 0,
      "y": 0,
      "z": 0,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    }
  }
```


## How to run Apollo modular testing on *GoMentumDTL* map

- Configure and run simulator
    - Map
        - Choose *GoMentumDTL* map
    - Vehicle
        - Choose *Lincoln2017MKZ* vehicle for Apollo
        - Attach `3D Ground Truth sensor` and `Signal sensor` with the sensor parameters above
        - Optionally, you can remove `Color Camera` and `Lidar` sensors from the sensor configuration as these sensors are not required for modular testing
    - Run simulation with `Random Traffic` enabled
- Launch Apollo following [these instructions](apollo-master-instructions)
    - Make sure that you're receiving ground truth messages via the perception topics on Apollo side
        - Topic for object detection: **/apollo/perception/obstacles**
        - Topic for traffic light detection: **/apollo/perception/traffic_light**
        - `cyber_monitor` for *CyberRT bridge* or `rostopic echo` for *ROS Apollo bridge*
    - In Dreamview, enable `Localization` module and verify if you can visually see that
        - your EGO vehicle is placed on the right position on the Apollo HD map for GoMentumDTL
        - bounding boxes for vehicles and pedestrians around the EGO vehicle (if any) including heading and velocity vectors
        - traffic light signals that are connected to the current lane of EGO vehicle are detected
    - Enable `Transform`, `Prediction`, `Planning`, `Routing`, and `Control`
        - Note that we are not running `Perception` and `Traffic Light` modules for modular testing
- Apollo should now be able to drive to your destination without running `Perception` modules

[![Compare Dreamview and Simulator](./images/modular-testing/compare-dreamview-and-simulator.png)](./images/modular-testing/full_size_images/compare-dreamview-and-simulator.png)
