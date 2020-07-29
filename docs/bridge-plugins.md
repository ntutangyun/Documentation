# <a name="top"></a>Bridge Plugins

Bridge plugins are custom bridge implementations that can be used by an ego vehicle to send and receive sensor data.
Bridge plugins must be built by the simulator and the resultant bundle named `bridge_XXX` must be placed in the `AssetBundles/Bridges` folder. If running the binary, this folder is included in the downloaded .zip file. 
If running in Unity Editor (Developer Mode), the sensor will be built into the folder directly. This must be done before running the simulator (running the executable or pressing `Play` in the Editor).

Building bridge plugins to a bundle is done as below:
1. Open `Simulator -> Build...` menu item
2. Select bridge plugins in "Bridges" section of build window
3. Build plugins with "Build" button

To make a bridge plugin, create folder in `Assets/External/Bridges`, for example `Assets/External/Bridge/CustomBridge`.

Inside this folder you must place all the scripts that will be used by simulator to build C# managed assembly.

## Implementation

A bridge plugin must provide two separate classes inheriting the following interfaces:

1) `IBridgeFactory`

2) `IBridgeInstance`

Factory class is used to provide meta-information about bridge and a few factory methods. It will be created only once.

Instance class represents the actual bridge implementation that is able to send and/or receive data. It will be created once for every vehicle where it is used.

### Bridge Factory

```
    public interface IBridgeFactory
    {
        IBridgeInstance CreateInstance();
        void Register(IBridgePlugin plugin);
        void RegPublisher<DataType, BridgeType>(IBridgePlugin plugin, Func<DataType, BridgeType> converter);
        void RegSubscriber<DataType, BridgeType>(IBridgePlugin plugin, Func<BridgeType, DataType> converter);
    }
```

This interface requires the class to expose four public methods:

1) `CreateInstance` - called to create new bridge instance. Typically this method simply returns new object of class
that implements IBridgeInstance interface.

2) `Register` - called by simulator allow factory register supported functionality with plugin interface. See the section below on plugin registration.

3) `RegPublisher` and `RegSubscriber` - called by custom sensors to register custom data types for publisher or subscriber functionality. Factory can use this method itself to register its supported data types for simulator built-in sensors.

Classes inheriting from this interface must have `BridgeName` attribute applied to inform name that will be
used to identify this bridge. For example:

```
    [BridgeName("MyCustomBridge")]
    public class MyCustomBridgeFactory : IBridgeFactory
    {
		...
	}
```

### Bridge Instance

```
    public interface IBridgeInstance
    {
        Status Status { get; }
        void Connect(string connection);
        void Disconnect();
    }
```

This interface requires the class to expose three public methods:

1) `Status` - getter that will be called by simulator to query status of bridge. For example, sensors will be sending data only if Status is set to `Connected` state.

1) `Connect` - called when simulator wants to initate new connection. Connection string is passed unmodified from web interface where it can be set to anything. It is responsibility of bridge implementation to validate then accept or reject this string.

2) `Disconnect` - called when simulator wants to close connection. Typically called upon unloading vehicle from scene.


## Bridge Plugin Registration

Upon creating a bridge factory, the simulator will call `Register` method to allow factory and pass plugin instance that factory should use to register available data types and publishers/subscribers. This information is not static as custom sensor plugins may want to register their custom types with one or multiple bridge factories.

Simulator provides many different data types, for example `ImageData` or `PointCloudData` that represents specific instances of data published or subscribed by sensors. Factory must inform plugin about which types it supports.
This is performed by calling `AddType<DataType>(string bridgeTypeName)` on plugin instance. `DataType` is one of the data types simulator supports. `bridgeTypeName` is an arbitrary string used to show name of this data type in UI.

For each subscriber or publisher bridge factory should call `AddSubscriberCreator<DataType>` or `AddPublisherCreator<DataType>` on plugin instance. These methods should provide delegate that can be used to create subscriber or publisher instances for each sensor that wants to use specific data type.

```
void AddSubscriberCreator<DataType>(SubscriberCreator<DataType> subscriber);
void AddPublisherCreator<DataType>(PublisherCreator<DataType> publisher);
```

See `IBridgePlugin.cs` source code for more information on delegate arguments and return values.

Typically, the bridge will call `AddType` + `AddSubscriberCreator` from `RegSubscriber` method, and `AddType` + `AddPublisherCreator` from `RegPublisher` method. This should be done for all default simulator data types simulator supports.

Additionally, custom sensor plugins may choose to call your bridge `RegPublisher` and `RegSubscriber` methods to add custom data types they which to publish or subscribe. Please refer to `ComfortSensor` and `LaneFollowingSensor` sensor plugins for examples of this functionality.


## Examples

Open-source examples are available:

[Logging Bridge](https://github.com/lgsvl/LoggingBridge) - simple bridge that logs all published data to a .txt file.

You can also refer to the existing ROS, ROS2 and CyberRT bridges in the main
[simulator repository](https://github.com/lgsvl/simulator/tree/master/Assets/Scripts/Bridge) 
for examples on how to make a fully featured bridge. 
