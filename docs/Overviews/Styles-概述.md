*这是七巧板的样式系统的概述。有关自定义样式创建系统的完整技术参考，请参见 [styles](../Syntax-Reference/styles.md)，有关具有这些样式的图形的所有技术细节，请参见[draw](../Syntax-Reference/draw.md).*

## draw styles

在场景文件中的两个位置引用了绘画样式: 当自定义 [styles](../Syntax-Reference/styles.md) 以及在 [draw](../Syntax-Reference/draw.md) 组中再次引用。

七巧板目前有五个内置基本的绘制样式: `polygons`, `lines`, `points`, `text`, 和 `raster`. 每种绘制样式均以不同的方式显示数据，其中一些样式需要特定的数据类型和属性。

对于每种基本绘制样式, 还可以使用`blend` 模式组合样式以及命名参数为 "blend_base", 例如 `translucent_polygons`, `overlay_lines` 等。
相关的更多信息，请查看 [`blend`](styles.md#blend-combo-styles).

#### `polygons`
`polygons` 镶嵌和拉伸矢量图形转换成3D几何形状。 它需要多边形数据。请参阅 [`polygons`](#polygons-1).

#### `lines`
`lines` 将多边形或线数据转换为线. 请参阅 [`lines`](#lines-1).

#### `points`
`points` 绘制一个实心圆在数据点的位置。它可以与点数据，线或面一起使用。 点将彼此“碰撞”, 仅由获胜者根据 [`priority`](../Syntax-Reference/draw.md#priority) 确定参数。

_[JS only]_ 仅来自同一数据源的点会相互碰撞。

从技术上讲，此绘制样式将创建一个小的四边形，然后使用默认的着色器为该四边形着色，以绘制一个点。可以使用自定义着色器或纹理覆盖此行为。

请参阅 [`points`](#points-1).

#### `text
`text` 在给定的点绘制文本标签。 它可以使用点，线或面数据。 与线条一起使用时，标签将沿着线条绘制。 与多边形一起使用时，标签将绘制在多边形的中心处。文本标签将彼此“碰撞”，仅绘制获胜者，具体取决于 [`priority`](../Syntax-Reference/draw.md#priority) 参数。

_[JS only]_ 仅来自同一数据源的文本元素会相互冲突。

请参阅 [`text`](#text-1).
 
#### `raster`
`raster` 绘制一个瓷砖尺寸的正方形瓷砖每与从相应的砖描绘 `Raster`的数据源。请参阅 [`raster`](#raster-1).

## Using Styles

这些内置的绘制样式用作七巧板中所有自定义样式的基础。在下方编写内联样式时`layer`, 可以通过以下两种方式之一在绘图组中引用它们:

一个自定义名称的绘制组可以引用`style`参数名字的样式:

```yaml
roads:
    draw:
        my_style:
            style: polygons
            color: blue
```

或者, 如果一个绘制组用绘制样式作为名字，那么`style`参数可以省略:

```yaml
roads:
    draw:
        polygons:
            color: blue
        lines:
            color: red
```

通过定义多个绘制组， 可以同时使用多种样式来绘制相同的特征：

```yaml
roads:
    draw:
        polygons:
            color: blue
        lines:
            color: red
```

定义自定义样式时，内置绘制组在新样式名称下使用 `base` 参数明确引用:

```yaml
styles:
    buildings:
        base: polygons
```

这样，自定义样式可以"扩展"行为或内置绘制样式。

## polygons
多边形绘制样式风格需要含有可通过线连接成一个"闭合"形状坐标的数据源。 如果多边形的线在不同位置开始和停止，则它是“开放”形状，那么 `polygons` 绘制样式无法使用它。 但是，如果一系列线连接回到其自己的起点，则将其视为"封闭"的，并且可以将其拉伸为3D形状。

#### `polygons` parameters
作为 `polygons` 绘制样式的扩展可以采用以下参数:

- `texcoords`
- `animated`
- `blend`
- `materials` : 查看 [materials](../Syntax-Reference/materials.md)
- `shaders`: 查看 [shaders](../Syntax-Reference/shaders.md)

#### `polygons` draw group requirements
使用 `polygons` 绘制样式的[Draw groups](../Syntax-Reference/draw.md#draw-group) 必须至少指定以下参数才能进行绘制:

- [color](../Syntax-Reference/draw.md#color)

## lines
*lines*样式需要含有连接坐标的数据源。因此，它可以接受线性或多边形输入数据。它沿着每个线段绘制一个矩形，并且可以选择绘制特殊样式 [`join`](../Syntax-Reference/draw.md#join) 和 [`cap`](../Syntax-Reference/draw.md#cap)

#### `lines` parameters
作为`lines` 绘制样式的扩展的样式可以采用以下参数:

- `texcoords`
- `animated`
- `blend`
- `materials` : 查看 [materials](../Syntax-Reference/materials.md)
- `shaders`: 查看 [shaders](../Syntax-Reference/shaders.md)

#### `lines` draw group requirements
使用 `lines` 绘制样式的 [Draw groups](../Syntax-Reference/draw.md#draw-group) 必须至少指定以下参数才能进行绘制:

- [color](../Syntax-Reference/draw.md#color)
- [width](../Syntax-Reference/draw.md#width)

## points
`points` 绘制样式被用来在感兴趣的`point`上画点或者画精灵。 它还可以在一个`point`上创建矩形， 并且有很多种着色方式:

- 用专门的着色画点的圈
- 使用 `texture`
- 使用 `sprite` 从这 `texture`

如果使用`point`画点，则可以在场景文件中使用 `size` 和 `color` 参数指定该点的圈大小和颜色。

`points` 和 `text` 具有特殊的关系, 这对于创建自定义标签和图标很有用。 它们也将相互碰撞-由  [`priority`](../Syntax-Reference/draw.md#priority) 参数确定“赢家”被抽出，而“失败者”未被抽出。

_[JS only]_ 仅来自同一数据源的点和文本元素会相互碰撞。

#### `points` draw group requirements
`points` 必须至少指定以下参数才能进行绘制:

- [color](../Syntax-Reference/draw.md#color)
- [size](../Syntax-Reference/draw.md#size)

## text
`text` 类似于 `sprites` 风格, 因为它在一个点建立一个矩形。但是，此样式不会使用自定义纹理进行着色，而是构建自己的包含文本的纹理。

文本的内容基于 [`text_source`](../Syntax-Reference/draw.md#text-source) 参数。 文本的样式由 [`font`](../Syntax-Reference/draw.md#font-parameters) 参数指定。

#### `text` parameters
`text` 样式扩展参数如下:

- [`font`](../Syntax-Reference/draw.md#font-parameters): Sets font's typeface, style, size, color, and outline.
- [`text_source`](../Syntax-Reference/draw.md#text_source): Determines label text, defaults to the feature's `name` property.
- [`priority`](../Syntax-Reference/draw.md#priority): Sets the priority of the label relative to other labels and points/sprites.
- [`align`](../Syntax-Reference/draw.md#align): Controls text alignment.
- [`anchor`](../Syntax-Reference/draw.md#anchor): Controls text's relative positioning.
- [`offset`](../Syntax-Reference/draw.md#offset): Controls text's position offset.
- [`text_wrap`](../Syntax-Reference/draw.md#text_wrap): Sets number of characters before text wraps to multiple lines.
- [`repeat_distance`](../Syntax-Reference/draw.md#repeat_distance): Sets the distance beyond which label text may repeat.
- [`repeat_group`](../Syntax-Reference/draw.md#repeat_group): Optional grouping mechanism for fine-grained control over text repetition.
- [`collide`](../Syntax-Reference/draw.md#collide): Sets whether label collides with other labels or points/sprites.
- [`move_into_tile`](../Syntax-Reference/draw.md#move_into_tile): Increases number of labels that will display, by moving some to fit within tile bounds (JS-only)

这些参数在 [draw](../Syntax-Reference/draw.md) 条目中.

#### `text` draw group requirements
使用 `text` 必须至少指定以下参数:

- [font](../Syntax-Reference/draw.md#font)

## `raster` 栅格
`raster` 样式渲染 [Raster data sources](../Syntax-Reference/sources.md#Raster), 如传统的光栅像素块。

请注意， `Raster` 通过使用 [rasters](../Syntax-Reference/sources.md#rasters) 参数将源“附加”到样式，源也可以被其他样式使用。 请参阅 [Rasters Overview](Raster-Overview.md).

## style composition with `mix`

`mix` 参数将命名样式（或多个样式）的属性复制到新样式。 这样，可以从现有样式中 "forked" 新样式。

这样就可以制作样式，它们之间的差异仅很小，而不必手动复制样式代码中的所有其他内容。它还允许一种样式充当"base"样式或"foundation"样式，并与其他样式混合。

以下示例通过复制"geo"样式的所有属性来创建名为"geo2"的样式：

```yaml
styles:
    geo:
        base: polygon
    geo2:
        mix: geo
```
这两种样式是相同的。

#### modifications

混合样式后，您可以添加或修改所需的任何属性。

例如，您可以创建一个名为styleB的新样式，该样式"继承自"一个称为styleA的现有样式，然后添加自定义着色器块:

```yaml
styleB:
   mix: styleA
   shaders:
      blocks:
         color: ...
```

或者您可以混合使用现有样式，但禁用照明:

```yaml
fancy-but-no-lighting:
    base: fancy
    lighting: false
```
您甚至可以修改混合样式`base`。例如，如果您有一个基于多边形的样式，并且具有要应用于行的自定义着色器块，则可以创建基于行的版本，如下所示:

```yaml
fancy-lines:
    mix: fancy-polygons
    base: lines # change the base to lines
```
请注意，在这种情况下，`polygons`仍将复制绘图样式特有的任何属性，但渲染器将忽略它们。


#### combinations

`mix`参数还可以给出样式列表-这使得它可以将多个特效混合在一起，例如，同时应用Windows和半色调效果:

```yaml
halftone-windows:
    mix: [ windows, halftone ]
```

列表中的样式将按照列出的顺序进行复制-因此，如果属性是多个命名样式的公用属性，则列表中最后一个命名的样式将优先。

```yaml
styles:
    custom:
        mix: [styleA, styleB, styleC]
```

在这里，styleC的属性将覆盖与其他列出的样式相同的任何属性。
