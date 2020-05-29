# Point Cloud Rendering
LGSVL Simulator supports scenes containing point cloud data. Point clouds can be rendered alongside other geometry like meshes or particles, are combatible with most of the features available in [high definition render pipeline](https://unity.com/srp/High-Definition-Render-Pipeline) and can be detected by multiple sensors, including lidar.

Point cloud rendering in Simulator can handle large point clouds (hundreds of millions of points) thanks to octree-based structure and selective, frustum-based culling.

If you want to create your own point cloud based environment, you should start with [importing](pointcloud-import.md) your point cloud data into simulator. This step is not necessary for pre-built point cloud environments - they do not require any additional setup.

## Rendering Features
Most of the features available for point cloud renderers are optional and can be toggled based on individual use cases. Lighting, for example, can be turned off if point cloud has light data already baked in, or calculated fully based on scene settings like time of day or weather.

Most notable features include:
- Full deferred lighting compatible with HDRP's lighting system
- Filling holes between points
- Rendering multiple point clouds at once
- Normals estimation
- Shadow casting/receiving
- Point streaming
- Point budget (for memory and render buffer)
- Camera frustum or distance-based culling
- Mesh collider generation
- Support for color/depth camera sensors and lidar

## Setup
The easiest way to set up point cloud rendering is to perform an [import](pointcloud-import.md) with the target scene open in the background. When importing is done, you will be prompted with a choice to automatically add all of the required components to the open scene. If accepted, point cloud with default rendering settings will immediately show up in scene view.

Alternatively, a prefab located in `Assets/Prefabs/PointCloudRenderer.prefab` can be added to any scene for a quick setup. This requires to specify point cloud data path in `NodeTreeLoader` component.

More complex setups with multiple point clouds or masked rendering require setting up components manually. Details can be found in [Components Overview](#components-overview) section.

## Components Overview
In the most common scenario with a single point cloud, one of each of the components described below will be present on a scene.

For more complex cases (e.g. multiple point clouds or separate color and intensity data) it's possible to add multiple instances of `NodeTreeLoader` and/or `NodeTreeRenderer` to one scene. Only one instance of `PointCloudManager` should ever be present.

### `PointCloudManager`
This component is responsible for keeping track of shared point cloud resources and orchestrating rendering for one or more point cloud renderers. It will also automatically set up HDRP passes and enable or disable them depending on all renderers' settings.

There are no settings available for this component. Only one instance should be active at a time. Disabling this component will disable rendering of all point cloud data.

### `NodeTreeLoader`
This component is responsible for managing single point cloud's data in memory. It will load chunks of point cloud data from mass storage based on each renderer's requests (performed on a separate thread) and will keep track of total amount of points loaded into memory. When defined point budget is exceeded, older points will be unloaded.

Single instance of this component can only operate on one point cloud, but multiple renderers can use data stored in a single `NodeTreeLoader`.

This component can also optionally load mesh colliders for a point cloud if they were generated during import process.

### `NodeTreeRenderer`
This component represents a single point cloud to render and stores all related settings. `Transform` to which this component is tied affects translation, rotation and scale of the point cloud.

By default, point cloud rendered by a single renderer will be visible in all cameras and compatible sensors, but not in shadow caster pass. This can be controlled on a per-renderer basis.

Note that this component will not actively trigger any rendering. It's used mostly as a container for data that will be used by `PointCloudManager`.

`NodeTreeRenderer` is default implementation that includes camera-based culling, which will make load requests to `NodeTreeLoader`. Adding other culling methods can be done by extending the base class, `PointCloudRenderer`.

## Rendering Modes Overview
Depending on the requirements, there are multiple combinations of rendering modes available. Sensors supported in point cloud rendering are compatible with all of them.

The most simple one (`Points`) will render points with size dependent on distance from the camera. This image is stable, but does not support lighting and will show holes if point cloud is not dense enough. Improved version of this mode (`Cones`) will use 3D cones instead of 2D circles, which creates better transitions between very close points, but has a slight performance cost.

More advanced rendering mode, `Solid`, will remove points that should be invisible from observer's position and interpolate color and depth data between leftover points. It can also reconstruct normals data from depth, and subsequently calculate lighting for point cloud. Filters used here are executed in screen space, which means that camera position and rotation affect rendering output. This might introduce some artifacts around edges, especially around large depth differences.

It's suggested to test available modes and choose the one best suited for given point cloud and use case. If lighting is required, solid rendering mode is the only one currently supporting it.

## Settings
### Loader Settings
|Parameter Name|Type|Description|
|:-:|:-:|:-:|
|Data Path|string|Directory in which imported point cloud data is stored. Paths starting with `.../` will be treated as relative to `Assets` folder in the project, others are treated as absolute.|
|Point Limit|int|Maximum amount of points that can be loaded into memory at once. These points are treated as cache between render buffer and hard drive. When point limit is exceeded, older points will be unloaded.|
|Load Meshes|bool|If this option is enabled, collider meshes will loaded alongside the point cloud data. This requires `Generate Mesh` option in [point cloud import](pointcloud-import.md) settings to be enabled.|

### Renderer Settings
Settings visible for renderer depend on currently selected rendering mode. Irrelevant settings will be hidden in inspector. Enabling additional features will sometimes show new settings related to them.

#### Shared Rendering Settings
|Parameter Name|Type|Description|
|:-:|:-:|:-:|
|Colorize|enum|Colorization mode to use for point cloud. If selected data is not present in source point cloud, result will be black. Available modes: `Colors`, `Intensity`, `Rainbow Intensity` and `Rainbow Height`.|
|Render Mode|enum|Describes how points should be represented. Available modes: `Points` (most simple one), `Cones` (similar to points, with interpolation) and `Solid` (fills holes, required for lighting).|
|Mask|enum|Enum flags used to enable or disable renderer's visibility. Point cloud will be visible only in selected passes. Available options: `Camera` (all color and depth cameras), `Shadowcaster` (enables shadow casting by point cloud), `Lidar` (makes point cloud detectable by lidar sensor).|
|Shadow Point Size|float|Only visible if `Shadowcaster` is enabled under `Mask`. Determines size of a point for shadow casting purposes.|
|Shadow Bias|float|Only visible if `Shadowcaster` is enabled under `Mask`. Determines shadow bias for each point. Value is relative to `Shadow Point Size`.|

#### Point/Cones Rendering Settings
|Parameter Name|Type|Description|
|:-:|:-:|:-:|
|Constant Size|bool|If this option is enabled, point size will not depend on distance from camera.|
|Pixel Size|float|Only visible with `Constant Size` enabled. Determines size of a point.|
|Absolute Size|float|Only visible with `Constant Size` disabled. Determines size of a point at a near plane. Points further away will be smaller.|
|Min Pixel Size|float|Only visible with `Constant Size` disabled. Determines minimum size of a point, irrelevant from distance.|

#### Solid Rendering Mode
Cascades options for Remove Hidden and Smooth Normals stages affect filter window size for both these effects and its scaling with distance for camera. They can be visualized by toggling `Show Preview` option on.

|Parameter Name|Type|Description|
|:-:|:-:|:-:|
|Lighting Mode|enum|Determines how light will affect the point cloud. Available modes: `Unlit` (no lighting, no shadows), `Shadow Receiver` (no lighting, receives shadows), `Full Deferred` (receives lighting and shadows)|
|Calculate Normals|bool|If enabled, normals approximation stage will be performed and world-space normals will be available. Required for lighting.|
|Smooth Normals|bool|If enabled, additional normals smoothing stage will be performed to reduce noise. Results will replace normals data from `Calculate Normals` option.|
|FoV Reprojection|bool|If enabled, wider image of point cloud will be rendered internally and it will be reprojected to desired camera FOV. This option reduces artifacts near the edges of the image. Does not affect final FOV.|
|Reprojection Ratio|float|Multiplier determining ratio between FOV used internally for `FoV Reprojection` option and original FOV of the camera.|

Solid rendering also has some debug options under separate tab. These are mostly directly affecting separate rendering stages, but some of them can me modified to affect final image.

|Parameter Name|Type|Description|
|:-:|:-:|:-:|
|Linear depth|bool|If enabled, linear depth will be used throughout all compute shaders instead of depth buffer values.|
|Force fill|bool|If enabled, holes below horizon line will be filled regardless of their size. Can create blurred artifacts for large holes, but removes all holes in road for low density point clouds.|
|Fill threshold|float|Sets height threshold under which `Force fill` option will be used if it's enabled.|
|Blend sky|bool|If enabled, point cloud color and intensity will be blended to sky color instead of black. Removes black color around edges, but requires additional sky pre-render for lit mode.|

#### Culling Settings
|Parameter Name|Type|Description|
|:-:|:-:|:-:|
|Node Tree Loader|NodeTreeLoader|Reference to loader that will be queried for point cache updates.|
|Cull Camera|Camera|Reference to camera used for culling. If this field is left empty, main simulator camera will be used after simulation is started.|
|Cull Mode|enum|Determines how points are evaluated for visibility. Available modes: `Camera Frustum` (both view frustum and distance from camera are considered), `Distance` (only distance from camera is considered - recommended if lidar is used).|
|Point Limit|int|Number of points that should be pushed to render buffer. This value determines maximum amount of points that are visible at once and directly affects buffer size.|
|Min Projection|float|Minimum screen projection size along single axis (in pixels) that octree node has to occupy to be traversed further. Nodes below this threshold will not be visible.|
|Rebuild Steps|int|Number of frames to spend for render buffer rebuild. Previous fully completed buffer will be used until new one is ready. Values higher than 1 reduce per-frame cost of buffer rebuild, but can introduce popping.|
