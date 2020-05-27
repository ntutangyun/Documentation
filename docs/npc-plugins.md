# NPC Plugins [](#top)

With NPC (non player character, that is non ego traffic) plugins we added the ability to customize the appearance and behavior of NPC vehicles in your simulations to allow you to add local variations of vehicle styles and brands or implement encounters with vehicles displaying distinguished behaviors such as erratic driving or vehicles stopping frequently like deliveries or trash pick up.

<h2> Table of Contents</h2>
[TOC]

## NPC Models

NPC Models allow customization of NPC visuals, most notably the 3D model of vehicles, but also the material used for the car paint or windows.
With supporting custom NPC models we also added the support for motorbikes and vehicles with more two axles as well as more than one steering axle as is found on heavy vehicles.

We classify NPCs into size categories which decide the spawning frequency as well as which paint colors are more common for which NPC size type.
The following table lists all currently implemented size types and the weight that correlates to their relative probability of encountering this type.

| NPC Size Type | exemplary spawning weight |
|---------------|---------------------------|
| Compact       | 5
| MidSize       | 6
| Luxury        | 2
| Sport         | 1
| LightTruck    | 2
| SUV           | 4
| MiniVan       | 3
| Large         | 2
| Emergency     | 1
| Bus           | 1
| Trailer       | 0
| Motorcycle    | 1

In this example, trailer type has a weight of zero, indicating that it should not spawn by itself, while MidSize type has the highest weight, indicating that it is the most frequent vehicle on the road. The NPC Type is configured via a [Component added to the root of the prefab](#npc-meta-data).

Similarily, colors for each spawned vehicle is picked from a table for each vehicle type.

In offline mode, NPC models are detected from `<simulator installation path>/AssetBundles/NPCs` and added to the simulation automatically.

## Building NPC Plugins [[top]] {: #building-npc-plugins data-toc-label='Building NPC Plugins'}

NPCs are grouped into collections to allow you to import regional collections of vehicles. This means that you can place e.g. `NorthAmericaNPCs` at  beneath the NPC source folder at `<simulator source root>/Assets/External/NPCs` to have all NorthAmericaNPCs vehicles show up in the build menu in a group.

To build AssetBundles of NPCs,

1. Open `Simulator -> Build...` menu item
2. Select behaviors in  e.g. "NPCs/NorthAmericaNPCs" section of build window
3. Build plugins with "Build" button

[![](images/build-npcs.png)](images/full_size_images/build-npcs.png)

The bundle named `npc_XXX` will be placed in the `AssetBundles/NPCs/<COLLECTION>` folder. If building the binary, this folder is included in the target destination.

See [build instructions](build-instructions) for more details.

## NPC Behaviors

NPC Behaviors allow NPC Vehicles to behave in custom ways different from the provided builtin NPC behavior and are enabled and configured through the Python API.

Before running the simulator (running the executable or pressing `Play` in the Editor) NPC behavior plugins must be built by the simulator using `Simulator -> Build...` menu and the built asset placed in the `AssetBundles/NPCs` folder.

Open-source example:

- [DrunkDriver](https://github.com/lgsvl/ExampleNPCs/DrunkDriver)

## Creating NPC Behavior Plugins [[top]] {: #creating-npc-behavior-plugins data-toc-label='Creating NPC Behavior Plugins'}

Create a folder in under your collection folder for each behavior, eg. `Assets/External/NPCs/ExampleNPCs/DrunkDriver`.

If your plugin is a pure behavior, that is it has no visual components, we require one C# file to have the same name as the folder under the collection folder, for example note how the name `DrunkDriver` repeats in both the folder name and file name: `Assets/External/NPCs/ExampleNPCs/DrunkDriver/DrunkDriver.cs`.

The main class has to inherit from `NPCBehaviourBase` and implement its abstract methods to hook into the NPC simulation framework. It is also possible to inherit from `NPCLaneFollowingBehaviour` if you want to implement a slight modification of the default lane following behavior, such as this simple `DrunkDriver` example:

```c#
using System.Collections;
using System.Collections.Generic;

using UnityEngine;
using Simulator.Api;
using Simulator.Map;
using Simulator.Utilities;
using SimpleJSON;

// In this example, we try to simulate a driver under ther influence of alcohol.
// We modify the default NPCLaneFollowingBehaviour because in principle this behavior
// shuld just add variation the the default behavior.
public class NPCDrunkDriverBehaviour : NPCLaneFollowBehaviour
{
    public float steerCorrectionMinTime = 0.0f;
    public float steerCorrectionMaxTime = 0.4f;

    public float steerDriftMin = 0.00f;
    public float steerDriftMax = 0.09f;
    
    protected float currentSteerDrift = 0.0f;
    protected float nextSteerCorrection = 0;
    // This function in the base class controls the NPC steering
    protected override void SetTargetTurn()
    {
        // we reduce the frequency at which steering is updated to the target heading to
        // simulate loss of attention or reduced reaction time
        if(nextSteerCorrection < Time.fixedTime)
        {
            float steerCorrectionIn = RandomGenerator.NextFloat(steerCorrectionMinTime, steerCorrectionMaxTime);
            nextSteerCorrection = Time.fixedTime + steerCorrectionIn;

            // we add drift to the steering to simulate loss of fine motor skills
            currentSteerDrift = RandomGenerator.NextFloat(steerDriftMin, steerDriftMax);
            currentSteerDrift = currentSteerDrift * Mathf.Abs(RandomGenerator.NextFloat(-1.0f, 1.0f));
            // we can reuse the base steering at reduced frequency
            base.SetTargetTurn();
        }
        else
        {
            // steering drift correlating to driving speed
            currentTurn += currentSteerDrift * currentSpeed;
        }
    }
}
```

If you want to configure aspects of your NPC behavior from the Python API, you can add a class implementing the `ICommand` interface as shown in this example:

```c#
class DrunkDriverControl : ICommand
{
    public string Name => "agent/drunk/config";

    public void Execute(JSONNode args)
    {
        var uid = args["uid"].Value;
        var api = ApiManager.Instance;

        if (!api.Agents.TryGetValue(uid, out GameObject npc))
        {
            api.SendError(this, $"Agent '{uid}' not found");
            return;
        }

        var behaviour = npc.GetComponent<NPCDrunkDriverBehaviour>();
        if (behaviour == null)
        {
            api.SendError(this, $"Agent '{uid}' is not a drunk driving NPC agent");
            return;
        }

        if(args.HasKey("correctionMinTime")) behaviour.steerCorrectionMinTime = args["correctionMinTime"].AsFloat;
        if(args.HasKey("correctionMaxTime")) behaviour.steerCorrectionMaxTime = args["correctionMaxTime"].AsFloat;
        if(args.HasKey("steerDriftMin")) behaviour.steerDriftMin = args["steerDriftMin"].AsFloat;
        if(args.HasKey("steerDriftMax")) behaviour.steerDriftMax = args["steerDriftMax"].AsFloat;

        api.SendResult(this);
    }
}
```

example usage from Python:

```python
# common setup code omitted, check PythonAPI/quickstart for examples
# you can check if it has been loaded:
behaviours = sim.available_npc_behaviours
for i in range(len(behaviours)):
    if behaviours[i]["name"]=="NPCDrunkDriverBehaviour": drunkDriverAvailable = True

#later...
npc = sim.add_agent(agent, lgsvl.AgentType.NPC, state)
if drunkDriverAvailable:
    inp = input("make drunk driver? yN")
if (inp == "y" or inp == "Y"):
    npc.set_behaviour("NPCDrunkDriverBehaviour")
    # usage of example command from C# plugin.
    npc.remote.command("agent/drunk/config", { "uid": npc.uid, "correctionMinTime":0.0, "correctionMaxTime":0.6, "steerDriftMin": 0.00, "steerDriftMax":0.09})

# can still use lane following methods as those are inherited
npc.follow_closest_lane(True, 5.6)
```

## Creating NPC Model Plugins [[top]] {: #creating-npc-model-plugins data-toc-label='Creating NPC Model Plugins'}

Create a folder in under your collection folder for each Model, eg. `Assets/External/NPCs/ExampleNPCs/DumpTruck`.
All assets of a NPC such as textures, additional materials or scripts are expected to live inside its folder.


[![](images/npc-components.png)](images/full_size_images/npc-components.png)

When an NPC is spawned, a NPCController script is attached to the root of the prefab instance, and it will try to find several components to animate the vehicle. To identify the components, we look at the Name property of the Components as follows:

| GameObject Name contains... | Component Type  | Description                                                                                      |
|--------------------|--------------------------|--------------------------------------------------------------------------------------------------|
| Head               | Light                    | headlights which are controlled by the NPCController to off, regular, high beam etc states       |
| Brake              | Light                    | braking lights, controlled by the NPCController to reflect the vehicle braking                   |
| IndicatorLeft      | Light                    | left indicator lights controlled by the NPCController when turning or when hazard lights are on  |
| IndicatorRight     | Light                    | right indicator lights controlled by the NPCController when turning or when hazard lights are on |
| IndicatorReverse   | Light                    | light turns on when NPC is reversing                                                             |
| Body               | Renderer                 | Renderer used to generate MeshCollider for collision and raycast checks                          |
| Body               | Material on any Renderer | this material will have its _BaseColor changed to give each NPC a random color from a list       |
| LightHead          | Material on any Renderer | a material which is controlled to emit light in conjunction with headlight Lights                |
| LightBrake         | Material on any Renderer | a material which is controlled to emit light in conjunction with brake Lights                    |
| IndicatorLeft      | Material on any Renderer | a material which is controlled to emit light in conjunction with left indicator Lights           |
| IndicatorRight     | Material on any Renderer | a material which is controlled to emit light in conjunction with right indicator Lights          |
| IndicatorReverse   | Material on any Renderer | a material which is controlled to emit light in conjunction with reversing Light                 |
| Wheel              | Renderer                 | Renderer is converted to Wheel. NPCs with less than four wheels are assumed to be bikes and will try to stay upright by applying a force each physics update |
| "Wheel" and "Front"| Renderer                 | wheels that are supposed to turn when steering                                                   |

### NPC Meta Data

Additional informaton about the NPC prefab is added by attaching a Unity `MonoBehaviour` Component called `NPCMetaData` added to each prefab.

[![](images/npc-metadata.png)](images/full_size_images/npc-metadata.png)

Currently, only NPC size type is specified here.

## Copyright and License [[top]] {: #copyright-and-license data-toc-label='Copyright and License'}


Copyright (c) 2020 LG Electronics, Inc.


This software contains code licensed as described in LICENSE.
