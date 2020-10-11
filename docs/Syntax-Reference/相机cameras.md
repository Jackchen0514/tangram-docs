*这是七巧板相机的技术文档。有关摄像头系统的概念概述，请参阅 [Cameras Overview](../Overviews/Cameras-Overview.md).*

#### `cameras`

该`cameras`元素是一个可选的顶级元素[scene file](../Overviews/Scene-File.md). 单个摄像机由该元素下的摄像机名称定义。
```yaml
cameras:
    camera1:
        type: perspective
    camera2:
        type: perspective
    overview:
        type: isometric
```

##### `camera`
`camera`如果场景中只有一个摄像机，也可以在顶层使用元素名称：

```yaml
camera:
    type: perspective
```

#### camera names
必需的字符串 ( [`camera`](cameras.md#camera) 除外). 可以是除 [reserved keywords](yaml.md#reserved-keywords).之外的任何内容。没有默认值。

```yaml
cameras:
    myCamera:
        type: perspective
    camera2:
        type: perspective
    lock-off:
        type: perspective
```
## common camera parameters

#### `type`
必需的字符串。其中一个 `perspective`, `isometric`, or `flat`. 没有默认值。
```yaml
cameras:
    camera1:
        type: perspective
    camera2:
        type: isometric
    overview:
        type: flat
```

#### `position`
可选 _[lat, lng]_ 或者 _[lat, lng, zoom]_. 没有默认值。

设置摄像机的经度和纬度（以度为单位）。
```yaml
camera1:
    position: [-73.97297501564027, 40.76434821445407]
```

#### `zoom`
可选数字 默认为: `15`

以标准[Web Mercator](http://en.wikipedia.org/wiki/Web_Mercator)缩放级别设置视图的缩放级别。

```yaml
camera1:
    zoom: 14
```

#### `active`
可选布尔值。 `true` or `false`. 没有默认值。

设置相机，该相机在首次加载时提供地图的活动视图。如果定义了多个摄像机，则一次只能激活一个摄像机。如果将多个摄像机设置为 `active: true`, 则行为将无法预测 (请查阅 [yaml#mappings](yaml.md#mappings)). 该 [JavaScript API](../API-Reference/Javascript-API.md) 可用于 [get](../API-Reference/Javascript-API.md#getactivecamera) 或 [set](../API-Reference/Javascript-API.md#setactivecamera_string_-camera) 活动的摄像头。

```yaml
camera1:
    active: false
```

#### `max_tilt`
[ES-only] 可选数字或 _[stops](yaml.md#stops)_. 度数.默认为 `90`. 

设置允许相机垂直倾斜的最大角度。对于`type: isometric`倾斜度较高的相机，在高变焦时还会受到限制，以防止观察平面与地面相交。

## perspective camera parameters

#### `focal_length`
可选数字或 _[stops](yaml.md#stops)_. 无单位. 默认为 `[[16, 2], [17, 2.5], [18, 3], [19, 4], [20, 6]]`.

设置z平面中的垂直夸大量。更改拉伸元素的外观高度。值越低=越夸大。另请参阅`fov`.

```yaml
camera1:
    focal_length: [[16, 2], [17, 2.5], [18, 3], [19, 4], [20, 6]]
```
#### `vanishing_point`
可选 _[number, number]_, 单位为 `px`. 默认 `[0px, 0px]`. 默认单位 `px`.

设置视在透视图的原点，以距屏幕中心的像素为单位。

```yaml
camera1:
    type: perspective
    vanishing_point: [-250, -250]
```

#### `fov`
可选数字

设置摄像机的“视野”，以度为单位。视场与以下项成反比`focal_length`：值越大，越夸张。如果两者都设置，`focal_length`将优先于`fov`。

```yaml
camera1:
    fov: 80
```

## isometric camera parameters

#### `axis`
可选 _[number, number]_. 默认: `[0, 1]`

设置 `[x, y]` 等轴测相机垂直轴的方向和数量，该轴和控件控制如何显示拉伸对象的高度。值`1`等于100％的小数位数。值越大，缩放越多。

```yaml
isometric-cam:
    type: isometric
    axis: [1, .5]
```

## flat camera parameters

所述`flat`相机呈现自顶向下的2D地图视图（挤出是不可见的），并且具有不唯一的参数。
