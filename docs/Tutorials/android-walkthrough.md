# Walkthrough: Add a Tangram map to an Android application

用 Tangram Android SDK, 您可以快速将精美实用的2D和3D地图添加到Android应用程序。 七巧板使用可自定义的场景，可以对地图外观的几乎每个方面进行精细控制，包括符号，照明，几何图形和要素标签。 Tangram Android SDK 是 [open source](https://github.com/tangrams/tangram-es), 并支持多种矢量数据格式。

请按照本指南将七巧板地图添加到您的Android应用程序。如果您正在寻找一种简单的方法来向您的应用程序添加预配置的地图和相关的地理服务， 你应该去看看 [Mapzen Android SDK](https://mapzen.com/documentation/android/).

Tangram Android SDK 可以使用任何Android构建系统，支持Gradle. 我们将使用本指南 [Android Studio](http://developer.android.com/sdk/index.html).

> 本指南将假定您对Android软件开发有一定的了解。 如果你想了解如何编写Android应用程序, 访问 [Android developer site](http://developer.android.com/training/index.html).

### Run the Simple Map demo application

首先，让我们看一个示例应用程序，该示例程序演示七巧板地图的简单用法。

首先下载七巧板Android演示库。您可以使用以下命令在终端窗口中执行此操作：

```
git clone --recursive https://github.com/tangrams/tangram-android-demos.git
```

接下来，通过打开Android Studio，选择“导入项目”并选择“ tangram-android-demos”文件夹来导入项目。

With the project open, choose `simplemap` from the run menu and run the demo on a device or an emulator. Your device will show a map that responds to dragging, pinching, and rotating gestures.

Finally, open the `simplemap` module in Android Studio to see the code used for the demo. In the next section, we will look at all of the steps needed to add a map like this to your application.

### Steps to add Tangram to an Android application

1. **Add Tangram as a Gradle dependency.** In the `build.gradle` file for your application's module, add the following dependency:

    ```
    compile 'com.mapzen.tangram:tangram:0.2.1'
    ```

2. **Add a scene file to your application assets.** The scene file is a YAML document that specifies the behavior and appearance of your map. You can write your own scene file or use one of the Mapzen styles like [Bubble Wrap](https://github.com/tangrams/bubble-wrap), [Cinnabar](https://github.com/tangrams/cinnabar-style), or [Refill](https://github.com/tangrams/refill-style). Add the scene file to your application's assets. In the demo applications, assets are specified in the Gradle build file:

    ```
    sourceSets.main.assets.srcDirs = ['../styles']
    ```

3. **Declare a MapView in your layout.** In your layout XML, add a View with the class `com.mapzen.tangram.MapView`. The XML used in the demo applications is:

    ```
    <com.mapzen.tangram.MapView
          android:id="@+id/map"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"/>
    ```

4. **Forward your Activity lifecycle events to the MapView.** For MapView to work correctly, your Activity must call the matching methods on MapView for lifecycle events, including `onCreate()`, `onResume()`, `onPause()`, `onDestroy()`, and `onLowMemory()`.

5. **Implement onMapReady.** Tangram maps are initialized asynchronously. When the map is ready for display and use, it calls `onMapReady(MapController)` on a listener that you give it. In this callback you can save a reference to the MapController and use it to begin interacting with the map.

6. **Initialize the map with getMapAsync.** With the MapView declared in your layout, you can start a background task that will prepare the map. Call `getMapAsync` on your MapView, providing a listener for the `onMapReady` callback and a String containing the path to your scene file within your application's assets.

That's it! You're ready to start using a Tangram map in your application.

### Using Mapzen's vector tile service

Mapzen provides a free vector tile service with GeoJSON, TopoJSON, and MVT tiles that you can use with Tangram. Mapzen scene files use this service as their data source. If you want to use Mapzen vector tiles for more than a demo, you will need to sign up for a free API key at [mapzen.com/developers](https://mapzen.com/developers). Then add your API key to the data source in your scene file:

```
sources:
  mapzen:
    type: MVT
    url:  https://tile.mapzen.com/mapzen/vector/v1/all/{z}/{x}/{y}.mvt
    url_params:
      api_key: [YOUR API KEY HERE]
```

> Tip: For Tangram on Android, MVT is usually the fastest and most efficient data format.

### Next steps

In this guide, you learned how to add a Tangram map to your Android application. Now you can:

 - Learn how to write or edit a Tangram [scene file](https://mapzen.com/documentation/tangram/Scene-file/),
 - See [more examples](https://github.com/tangrams/tangram-android-demos) of things your application can do with the map, or
 - Look at the [reference documentation](https://mapzen.com/documentation/tangram/android-sdk/0.2.1/) for the Tangram Android SDK.
