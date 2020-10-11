该**scene file**场景文件是一个[YAML](http://en.wikipedia.org/wiki/YAML)文件，其中组织所有七巧板用于绘制地图的元素。YAML是一种与JSON相似的数据格式，但是它具有一些独特的功能，我们认为这些功能使其更友好，更易于使用。（有关这些功能的更多信息，请参见[YAML](../Syntax-Reference/yaml.md)条目。）

## Top-level Elements

场景文件中允许使用各种顶级元素。每个定义了以元素命名的块的开头。

#### `cameras`
可选元素。该 `cameras`块允许修改地图视图。

See [Cameras Overview](Cameras-Overview.md) and [cameras](../Syntax-Reference/cameras.md).

#### `layers`
必填元素。该`layers`块将数据划分为多个层并分配样式参数。

See [Styles Overview](Styles-Overview.md) and [layers](../Syntax-Reference/layers.md).

#### `global`
可选元素。该`global`块允许添加自定义命名参数，这些参数可以替换场景文件中其他位置的值。

See [global](../Syntax-Reference/global.md).

#### `import`
可选元素。该`import`块允许通过简单的基于文本的合并，将其他.yaml文件（包含从顶级块向外指定的七巧板场景块的任意组合，直至整个场景文件）导入当前场景。

See [import](../Syntax-Reference/import.md).

#### `lights`
可选元素。该`lights`块允许控制地图的照明。

See [Lights Overview](Lights-Overview.md) and [lights](../Syntax-Reference/lights.md).

#### `scene`
可选元素。该`scene`块设置各种场景范围的参数。

See [scene](../Syntax-Reference/scene.md).

#### `sources`
必填元素。该`sources`块标识数据源。

See [sources](../Syntax-Reference/sources.md).

#### `styles`
可选元素。该`styles`块定义了渲染样式，由[materials](../Syntax-Reference/materials.md)和[shaders](../Syntax-Reference/shaders.md)组成。

See [Styles Overview](Styles-Overview.md) and [styles](../Syntax-Reference/styles.md).

#### `textures`
可选元素。该`textures`块允许在 [materials](../Syntax-Reference/materials.md)中对纹理进行高级配置。

See [Materials Overview](Materials-Overview.md) and [textures](../Syntax-Reference/textures.md).


## The basics
要创建地图，场景文件仅需要：

- a data source
- data interpretation rules (filters)
- styling rules

这是一个简单的场景文件：

```yaml
sources:
    nextzen:
        type: TopoJSON
        url: https://tile.nextzen.org/tilezen/vector/v1/256/all/{z}/{x}/{y}.topojson

layers:
    earth:
        data: { source: nextzen }
        draw:
            polygons:
                order: 0
                color: darkgreen
    water:
        data: { source: nextzen }
        draw:
            polygons:
                order: 1
                color: lightblue

    roads:
        data: { source: nextzen }
        draw:
            lines:
                order: 2
                color: white
                width: 1.5px
```

在此示例中，包括了所有三个元素–这将生成一张地图。
