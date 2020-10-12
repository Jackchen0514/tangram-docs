*这是七巧板块的技术文档 `fonts` 。有关七巧板标签功能的概述，请参见 [`text`](../Overviews/Styles-Overview.md#text-1) 条目中的 [Styles Overview](../Overviews/Styles-Overview.md).*

## `fonts`
该 `fonts` 元素是一个可选的顶级元素 [scene file](../Overviews/Scene-File.md). 它只有一种子元素：命名字体定义。

字体定义可以定义在以下两种方式之一的字体：通过外部的CSS，或与任何数量的 `font face definitions`.

#### `external`
[[JS-only](https://github.com/tangrams/tangram)] 使用这种定义字体的方法，字体定义的值设置为 "`external`":

```
fonts:
    Poiret One: external
```

这要求在HTML中进行相应的CSS声明:

`<link href='https://fonts.googleapis.com/css?family=Poiret+One' rel='stylesheet' type='text/css'>`

七巧板将按名称标识外部加载的字体，并使其可用于文本标签。

#### font face definition
字型定义可以用作所述的值的字体定义。这是一个具有许多可能参数的对象:

  - `weight`: 默认为 `normal`，也可以为 `bold` 或数字字体粗细，例如 `500`
  - `style`: 默认为 `normal`，也可能是 `italic`
  - `url`: 从中加载字体的URL。为了获得最大的浏览器兼容性，字体应该是 `ttf`，`otf`, 或 `woff` (`woff2` 是 [目前支持的](http://caniuse.com/#search=woff2) 在Chrome和Firefox而不是其他的主流浏览器).与其他场景资源一样， `url` 相对于场景文件。

具有单个参数url的字体定义的示例:

```yaml
fonts:
    Montserrat:
        url: https://fonts.gstatic.com/s/montserrat/v7/zhcz-_WihjSQC0oHJ9TCYL3hpw3pgy2gAi-Ip7WPMi0.woff
```

具有多个参数的字体定义的示例:

```
fonts:
    Open Sans:
        - weight: 300
          url: fonts/open sans-300normal.ttf
        - weight: 300
          style: italic
          url: fonts/open sans-300italic.ttf
        - weight: 400
          url: fonts/open sans-400normal.ttf
        - weight: 400
          style: italic
          url: fonts/open sans-400italic.ttf
        - weight: 600
          url: fonts/open sans-600normal.ttf
        - weight: 600
          style: italic
          url: fonts/open sans-600italic.ttf
        - weight: 700
          url: fonts/open sans-700normal.ttf
        - weight: 700
          style: italic
          url: fonts/open sans-700italic.ttf
        - weight: 800
          url: fonts/open sans-800normal.ttf
        - weight: 800
          style: italic
          url: fonts/open sans-800italic.ttf
```

当以这种方式声明字体定义时，在样式块中指定font-family，style和weight参数的适当组合时，将使用关联的url的字体 [`font`](draw.md#font) 代码块:

```yaml
draw:
    text:
        font:
            family: Open Sans
            style: italic
            weight: 300
```

有关更多信息，请参见 [font parameters](draw.md#font-parameters).
