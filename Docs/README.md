# Docs Home

说明：该目录基本是API，我常用的API收录在了fabric.js_API页。

### fabric 4.3.1

### Running this repo locally(在本地运行此仓库)

此仓库使用 [Jekyll](http://jekyllrb.com/)服务页面，可在 [此处](http://jekyllrb.com/docs/installation/#install-with-rubygems)安装。 

安装完成后，只需在终端中此仓库的根目录下运行`jekyll serve`命令即可。在此命令的控制台输出中，您将看到服务器地址：<base_url>，在浏览器中访问此base_url，您将在fabricjs.com上看到应用了本地更改的内容。

### Not familiar with Fabric?(不熟悉Fabric？)

最好的起点是通过 **[4-part tutorial](http://fabricjs.com/articles)**/[4部分教程](https://github.com/eternitywith/fabric.js-docs-cn/tree/master/Tutorial)

### How to navigate the docs?(如何浏览文档？)

这是Fabric的对象模型的层次结构，以使其更容易：

#### Main rendering surface(主渲染)

- [fabric.Canvas](http://fabricjs.com/docs/fabric.Canvas.html)
- [fabric.StaticCanvas](http://fabricjs.com/docs/fabric.StaticCanvas.html)

#### Things to fill objects with(填充相关)

- [fabric.Color](http://fabricjs.com/docs/fabric.Color.html)
- [fabric.Gradient](http://fabricjs.com/docs/fabric.Gradient.html)
- [fabric.Pattern](http://fabricjs.com/docs/fabric.Pattern.html)
- [fabric.Shadow](http://fabricjs.com/docs/fabric.Shadow.html)

#### 2D shapes inheriting from fabric.Object(从fabric.Object继承的2D形状)

- fabric.Object
  - [fabric.Line](http://fabricjs.com/docs/fabric.Line.html)
  - [fabric.Circle](http://fabricjs.com/docs/fabric.Circle.html)
  - [fabric.Triangle](http://fabricjs.com/docs/fabric.Triangle.html)
  - [fabric.Ellipse](http://fabricjs.com/docs/fabric.Triangle.html)
  - [fabric.Rect](http://fabricjs.com/docs/fabric.Rect.html)
  - [fabric.Polyline](http://fabricjs.com/docs/fabric.Polyline.html)
  - [fabric.Polygon](http://fabricjs.com/docs/fabric.Polygon.html)
  - [fabric.Group](http://fabricjs.com/docs/fabric.Group.html)
  - fabric.Text
    - [fabric.IText](http://fabricjs.com/docs/fabric.IText.html)
  - fabric.Image
    - fabric.Image.filters
      - [BaseFilter](http://fabricjs.com/docs/fabric.Image.filters.BaseFilter.html)
      - [Brightness](http://fabricjs.com/docs/fabric.Image.filters.Brightness.html)
      - [Convolute](http://fabricjs.com/docs/fabric.Image.filters.Convolute.html)
      - [GradientTransparency](http://fabricjs.com/docs/fabric.Image.filters.GradientTransparency.html)
      - [Grayscale](http://fabricjs.com/docs/fabric.Image.filters.Grayscale.html)
      - [Invert](http://fabricjs.com/docs/fabric.Image.filters.Invert.html)
      - [Mask](http://fabricjs.com/docs/fabric.Image.filters.Mask.html)
      - [Noise](http://fabricjs.com/docs/fabric.Image.filters.Noise.html)
      - [Pixelate](http://fabricjs.com/docs/fabric.Image.filters.Pixelate.html)
      - [RemoveWhite](http://fabricjs.com/docs/fabric.Image.filters.RemoveWhite.html)
      - [Sepia](http://fabricjs.com/docs/fabric.Image.filters.Sepia.html)
      - [Sepia2](http://fabricjs.com/docs/fabric.Image.filters.Sepia2.html)
      - [Tint](http://fabricjs.com/docs/fabric.Image.filters.Tint.html)
  - fabric.Path
    - [fabric.PathGroup](http://fabricjs.com/docs/fabric.PathGroup.html)

#### 2D entities NOT inheriting from fabric.Object(不从fabric.Object继承的2D实体)

- [fabric.Point](http://fabricjs.com/docs/fabric.Point.html)
- [fabric.Intersection](http://fabricjs.com/docs/fabric.Intersection.html)

#### Main fabric namespace(fabric主要命名空间)

- fabric
  - [fabric.Observable](http://fabricjs.com/docs/fabric.Observable.html)
  - [fabric.Collection](http://fabricjs.com/docs/fabric.Collection.html)

#### Fabric utilities and helpers(Fabric工具函数)

- fabric.util
  - [fabric.util.object](http://fabricjs.com/docs/fabric.util.object.html)
  - [fabric.util.array](http://fabricjs.com/docs/fabric.util.array.html)
  - [fabric.util.string](http://fabricjs.com/docs/fabric.util.string.html)
  - [fabric.util.ease](http://fabricjs.com/docs/fabric.util.ease.html)

#### Fabric freedrawing brushes(Fabric自由绘画刷)

- fabric.BaseBrush
  - [fabric.PencilBrush](http://fabricjs.com/docs/fabric.PencilBrush.html)
  - [fabric.CircleBrush](http://fabricjs.com/docs/fabric.CircleBrush.html)
  - [fabric.SprayBrush](http://fabricjs.com/docs/fabric.SprayBrush.html)
  - [fabric.PatternBrush](http://fabricjs.com/docs/fabric.PatternBrush.html)