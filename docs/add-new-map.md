# How to Add a New Map

This tutorial works with LGSVL Simulator release [2019.05](https://github.com/lgsvl/simulator/releases/tag/2019.05).

This document will describe how to create a new map in the LGSVL Simulator.

## Video

<div class="video-container">
<iframe  width="696" height="391" src="https://www.youtube.com/embed/S0w2ahhEPbE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
([Link](https://youtu.be/S0w2ahhEPbE)) Adding a new map in LGSVL Simulator.
<br>
<br>

## Getting Started

The following text is a list of the steps described in the above  video.

1. Launch LGSVL Simulator from the Unity Editor (as described [here](getting-started.md)).
1. In the top menu, select File -> New Scene or Ctrl+N
1. Create your new map folders in Assets/External/Environments/NewMap with /Models/Materials and /Prefabs in this root folder
1. It is helpful to clone the CubeTown repository as an example scene to see how all Simulator features are setup and assets organized.

## Import Assets

1. Import assets you created or obtained into the Unity Editor.
1. Move models, textures, materials and any other assets you need into the /NewMap folders.  The new map should have no asset dependencies from folders outside of /NewMap.
1. Do not include script files or non-HDRP shaders.  If you need scripts in your scene, you will need to place in the source files of the LGSVL Simulator and build your own binaries.
1. If your assets have non-standard shaders you will need to change them to the Unity Standard shader before converting to HDRP.
1. Once all non Standard shaders are changed, you can convert the materials to HDRP materials.
1. Select all materials.
1. Select Edit -> Render Pipeline -> Upgrade Selected Materials to High Definition Materials.  If the materials are all using Standard shaders, all shader settings will be converted with little need for adjustment.
1. Be sure all materials have GPU instancing checked.
1. Now we can start building the new map.

## Creating the scene

1. Delete the MapHolder object in the Hierarchy Panel. This will be added back with the annotation tool later.
2. Create an Object to hold the mesh data for your map.  Name it the same as your folder, e.g. NewMap. Zero out the transform position/rotation: 0,0,0 and scale: 1,1,1.
3. Save the scene in the root of the /NewMap, naming  _exactly_ the same, NewMap.scene.  Delete or move any other scene files out of this root folder. 
4. Open the Annotation Tool, select Simulator -> Annotation Tool, and click Create Map Holder.  This will add the correct holder for annotation named already the same as your map scene name.
5. Save the scene again. Do this often. You should always be hitting Ctrl+S during development.
6. Create the scene by adding meshes and colliders to the NewMap mesh holder object as children. You can further organize by making secondary holder objects, e.g., Roads, Buildings, Props.  Just be sure to zero transforms for holder objects.
7. Once organized and positioned, select the root holder object and check static in the top right of the Inspector Panel. Then, open the static menu and uncheck Off Mesh Link Generation and Batching Static. HDRP supports GPU instancing materials OR Batching, not both. GPU instancing is more performant.
8. Then select all mesh holder root objects that you do not want pedestrians to walk on, e.g., Props, Buildings, Signs, and uncheck Navigation Static. This will make Nav Meshes ignore these mesh types.
9. Add the TimeOfDayLight.cs component to all meshes that will emit light, not building windows.  Then add a light component object as a child of this light.
10. Add TimeOfDayBuilding.cs to the building holder.
11. Be sure you apply black textures to the emission component of the meshes you don't want to emit and create an emission map for the ones you do want to light during the night.
12. Set semantic tags for the holder objects OR the objects themselves, not both.  Code will search and apply tags to materials from parent, down to all child objects.
13. Set physics layers for all meshes to default.  Be sure to do this from the root object so all child objects get set.  Purchased assets may have very odd tags and physics layers that you will need to fix.
14. Change all road material shaders to ShaderGraphs -> EnvironmentSimulation for environmental effects to work on road surfaces.
15. Add mesh colliders to all meshes that need physics collisions, especially roads.

## Extra Steps not in video

1. Add the RainVolume.cs to the mesh of the road or empty objects in the scene to have rain particles spawn in the map.
2. Select Window -> AI -> Navigation.
3. Select Object tab in this panel.
4. Select groups of meshes, not their holders, e.g., road, sidewalk, grass, curb meshes.
5. In the Object tab in Navigation, set the meshes to the types.
6. Select the Bake tab.
7. Click the Bake button.  This creates a nav mesh that gets saved in the /NewMap folder.
8. Select Window -> Rendering -> Occlusion Culling.
9. Select Bake tab.
10. Click the Bake button.  This creates an occlusion object that helps with frame rate.

### Finalize other map features

1. Select MapOrigin object in the Hierarchy Panel.
2. Set Northing, Easting, UTM and TimeZone.
3. Set NPC settings for the map.
4. Edit SpawnInfo position and rotation.  This object(s) will determine where the EGO vehicle(s) will spawn.
5. Save the scene.
6. Select Simulator -> Build...
7. Select NewMap and build your amazing map.

Thanks for following this tutorial!

