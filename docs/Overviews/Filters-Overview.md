七巧板设计用于多种格式的矢量切片。 数据源在 [`sources`](../Syntax-Reference/sources.md) 七巧板的场景文件代码块中指定。 一旦指定了数据源， **filters** 可以让您以不同的方式设置数据的不同部分的样式

七巧板场景文件以两种方式过滤数据: 顶层过滤器**layer filters** 和 底层特征过滤器 **feature filters**.

# Layer filters

矢量图块通常包含可被视为 "layers" 的顶层结构-在GeoJSON文件中, 这些将是FeatureCollection对象。在七巧板场景文件中，  [`layers`](../Syntax-Reference/layers.md) 对象允许您通过与图层名称匹配来按图层拆分数据。

```yaml
layers:
    my-roads-layer:
        data:
            source: nextzen
            layer: roads
        draw: ...
```

`layer: roads` 在 [`data`](../Syntax-Reference/layers.md#data) 块中指定与此GeoJSON对象匹配：

```json
{"roads":
    {"type":"FeatureCollection","features":[
        {"geometry":"..."}
    ]}
}
```

## Layer name shortcut

如果`layer`未指定过滤器，则七巧板将尝试使用图层名称作为过滤器。在此示例中，图层名称 "roads" 与数据中的图层匹配:

```yaml
layers:
    roads:
        data:
            source: nextzen
        draw: ...
```

# Feature filters

一旦应用了顶层 `layer` 过滤器, [`filter`](../Syntax-Reference/layers.md#filter) 就可以定义要素对象以按要素属性进行过滤，以进一步缩小所需数据的范围并优化应用于该数据的样式。
```yaml
layers:
    roads:
        data: { source: nextzen }

        highway:
            filter:
                kind: highway
            draw: ...
```
这里，在"osm"数据源中"roads"匹配一个顶层的"roads"。它有一个应用于'roads'层中所有要素的'style'块，除非被覆盖，否则用作一种'default'样式。

然后，声明一个名为'highway'的子图层，并带有自己的`filter` 和 `draw`。它的'draw'块仅适用于与其匹配'filter'的属性为'kind',其值为'highway'的道路

## Inheritance

较高级别的过滤器继续在较低级别上应用，这意味着较高级别的 `draw` 较高级别的过滤器继续在较低级别上应用，这意味着较高级别的 `draw` 参数。

使用子图层和继承，您可以指定越来越具体的过滤器和绘制样式，以解决您喜欢的多种特殊情况。

# Matching

在 `layer` 里的每个特征首先匹配每个顶层的'filter', 如果该要素的数据与过滤器匹配, 则会为该要素分配任何关联的 [`draw`](../Syntax-Reference/draw.md) 样式, 并将其传递给任何子曾. 如果任何子层过滤器与功能匹配, 则该子层的 `draw` 样式将覆盖那些匹配特征的所有先前分配的样式规则，依此类推。

特征过滤器可以匹配数据中任何命名的特征属性，以及一些特殊的保留关键字。

## Feature properties

GeoJSON数据源中的要素属性在特别称为"properties"的JSON成员中列出:

```json
{
    "type": "Feature",
    "id": "248156318",
    "properties": {
        "kind": "commercial",
        "area": 12148,
        "height": 63.4000000
    }
```

类似的属性结构也存在于其他数据格式中，例如TopoJSON和Mapbox Vector Tiles。Tangram使这些结构可按`filter`属性名称用于块，也可用于'feature'关键字下的任何JavaScript过滤器函数。

上面的json功能将匹配以下两个过滤器:

```yaml
filter:
    kind: commercial

filter: function() { return feature.kind == "commercial"; }
```

特征过滤器最简单的类型是有关特征的一个命名属性的声明。

过滤器可以匹配精确值:

```yaml
filter:
    kind: residential
```

列表中的任何值:

```yaml
filter:
    kind: [residential, commercial]
```

或数值范围内的值:

```yaml
filter:
    area: { min: 100, max: 500 }
```

布尔值"true"将传递包含命名属性的功能，而忽略该属性的值。值为"false"将传递不包含命名属性的功能:

```yaml
filter:
    kind: true
    area: false
```

要匹配其值为布尔值的属性，请使用列表语法:

```yaml
filter:
    boolean_property: [true]
```

功能过滤器还可以评估JavaScript函数中的一个或多个属性:

```yaml
filter:
    function() { return feature.area > 100000 }
```

例如，假设我们有一个功能，该功能具有一个称为"height"的属性:

```json
{ "type":"Feature", "properties":{ "height":200 } }
```

此功能将与以下过滤器匹配:

```yaml
filter: { height: 200 }
filter: { height: { max: 300 } }
filter: { height: true }
filter: { unicycle: false }
filter: function() { return feature.height >= 100; }
filter: function() { return true; }
```

并且将不符合以下过滤条件:

```yaml
filter: { height: 100 }
filter: { height: { min: 300 } }
filter: { height: false }
filter: { unicycle: true }
filter: function() { return feature.height <= 100; }
filter: function() { return false; }
```

## Keyword properties

关键词`$geometry`匹配几何特征类型，有这样的情况当一个FeatureCollection包含多于一种几何类型，有效的几何类型为:

- `point`: 匹配 `Point`, `MultiPoint`
- `line`: 匹配 `LineString`, `MultiLineString`
- `polygon`: 匹配 `Polygon`, `MultiPolygon`

```yaml
filter: { $geometry: polygon }                      # matches polygons only

filter: { $geometry: [point, line] }                # matches points and lines, but not polygons

filter: function() { return $geometry === 'line' }  # matches lines only
```

在数据层包含多个源层的情况下，关键字`$layer`与特征的层名称匹配。在以下情况下，将从两个源层创建一个数据层， 然后可以再次将它们分割为样式:

```yaml
labels:
    data: { source: nextzen, layer: [places, pois] }
    draw:
        ...
    pois-only:
        filter: { $layer: pois }            # matches features from the "pois" layer only
        draw:
            ...
```

关键字`$zoom` 与地图的当前缩放级别匹配。 它可以与特定值或 `min` 和 `max` 参数一起使用。

```yaml
filter: { $zoom: 14 }                       # matches only zoom 14

filter: { $zoom: { min: 10 } }              # matches zooms 10 and up

filter: { $zoom: { min: 12, max: 15 } }     # matches zooms 12-14

filter: { $zoom: [8, 9] }                   # matches only zooms 8 and 9

filter: function() { return $zoom <= 10 }   # matches zooms 10 and below
```

## `label_placement`

这个特殊的属性`label_placement`仅仅被用来点的几何形状，通过在非平铺的数据源上将`generate_label_centroids`设置为`true`。这个选项在数据源的每个多边形的几何中心创建了一个新的重心点。然后,
将`label_placement`设置成`true`，可以在每个多边形要素的中心绘制一个标签，而不是每个图块一个标签，这是默认行为。在数据源中增加这些重心点，在 [source] 代码块中添加`generate_label_centroids`属性:

```yaml
sources:
    nextzen:
        type: TopoJSON
        url: https://your.data.com/example.topojson
        generate_label_centroids: true

layers:
    landuse:
        data: {source: nextzen}
        points:
            filter:
                label_placement: true
```

请查阅 [`generate_label_centroids`](../Syntax-Reference/sources.md#generate_label_centroids)

## Dot notation

点符号`.` 可以用于访问嵌套的要素属性，这些属性可以在GeoJSON / TopoJSON源中进行编码，也可以从MVT字符串化的JSON属性进行解析。也可以通过自定义JS过滤器功能访问这些属性。

给定一个属性 `a: { b: { c: 'test' } }`, 此过滤器将匹配:
`filter: { a.b.c: test }`

特征属性的名称中包含`.`可以通过`\.`来转义。例如，一个名为`'d.e.f': 'escaped'`特征属性可以用`filter: { d\.e\.f: escaped }`来匹配

这些可以混合使用，例如， 某个属性`{ 'a.b': { c: 'mixed' }` 可以用`filter: { a\.b.c: mixed }`来匹配。

## Filter functions

### Range functions

过滤器函数 `min` 和 `max` 等效于JavaScript方法中的 `>=` 和 `<` , 并且可以组合使用。

```yaml
filter:
    area: { max: 1000 } }      # matches areas below 1000 sq meters

filter:
    height: { min: 70 } }       # matches heights 70 and up

filter:
    $zoom: { min: 5, max: 10 }  # matches zooms 5-9
```

### Array functions

- `includes_any`: 检查一个数组 `a` 是否包含一个或多个`p`, `q`, `r` 值:

`filter: { a: { includes_any: [p, q, r] } }`

举个例子:

```yaml
filter:
    kind: { includes_any: [bus, rail, road] }
```

- `includes_all`: 检查`a`数组是否包含所有`p`, `q`, `r`值: 

`filter: { a: { includes_all: [p, q, r] } }`

举个例子:
```yaml
filter:
    kind: { includes_all: [bus, rail, road] }
```


### `px2`

范围功能还可以接受特殊的屏幕空间区域单位 `px2`:

```yaml
filter: { area: { min: 500px2 } }
```

本示例通过按当前缩放级别覆盖500像素屏幕区域的墨卡特米的数量来过滤特征的area属性。这意味着该区域属性必须为平方米墨卡特米，就像Mapzen矢量图块提供的属性一样。

与场景文件中其他基于像素的值一样，`px2`单位以逻辑像素（或“ CSS像素”）表示，这意味着它们以1的像素密度进行解释，并自动按比例放大以显示更高的密度。

该`px2`单元的语法可用于简化更加麻烦每缩放滤波器。

请注意，`px2`只有在数据源已经包含适当的area属性的情况下，才可以应用area过滤器-不必将其命名为area，因为可以在过滤器中指定任何属性名称，但是它必须已经存在于数据源中。


## Boolean functions

以下布尔过滤器功能也可用:

- `not`
- `any`
- `all`
- `none` (a combination of `not` and `any`)

`not` 将单个过滤器对象作为其输入:

```yaml
filter:
    not: { kind: restaurant }

filter:
    not: { kind: [bar, pub] }
```

`any`, `all`, 和 `none` 获取过滤器对象列表:

```yaml
filter:
    all:
        - { kind: museum }
        - function() { return feature.area > 100000 }

filter:
    any:
        - { height: { min: 100 } }
        - { name: true }

filter:
    none:
        - { kind: cemetery }
        - { kind: graveyard }
        - { kind: aerodrome }
```

### Lists imply `any`, Mappings imply `all`

几个过滤器的列表是使用该 `any` 功能的快捷方式。这两个过滤器是等效的:

```yaml
filter: [ kind: minor_road, railway: true ]

filter:
    any:
        - kind: minor_road
        - railway: true
```

几个过滤器的映射是使用该`all`能的捷径。这两个过滤器是等效的:

```yaml
filter: { kind: hamlet, $zoom: { min: 13 } }

filter:
    all:
        - kind: hamlet
        - $zoom: { min: 13 }
```

## Matching collisions

在某些情况下，相同级别的过滤器可能会返回重叠的结果:

```yaml
roads:
    data: { source: nextzen }
    highway:
        filter: { kind: highway }
        draw: { lines: { color: red } }
    bridges:
        filter: { is_bridge: yes }
        draw: { lines: { color: blue } }
```

在这种情况下，“高速公路”的颜色为红色，"桥梁"的颜色为蓝色。但是，如果任何特征既是"高速公路"又是"桥梁"，则它将匹配两次。由于YAML映射在技术上是"无序的"，因此无法保证其中一种样式会始终显示在另一种样式上。这里的解决方案是重组样式，以便每种情况都明确匹配:


```yaml
roads:
    highway:
        filter: { kind: highway }
        draw: { lines: { color: red } }
        highway-bridges:
            filter: { is_bridge: yes }
            draw: { lines: { color: blue } }
    other-bridges:
        filter: { is_bridge: yes, not: { kind: highway} }
        draw: { lines: { color: green } }
```
