## Fabric.js demos · Custom controls, polygon(Fabric.js演示·自定义控件，多边形)

该demo演示了如何使用控件api进行更改多边形形状的操作。由于需要了解内部多边形逻辑，定位点和转换，因此通常很难把握。

在demo中，画布具有缩放和平移功能，因此我们确定我们的代码足够通用。出于相同的原因，多边形也被赋予了较大的strokeWidth。 

我们具有触发编辑模式的功能。当进入编辑模式时，我们为每个多边形点创建一个新控件。在这些控件上，我们附加了一个称为点索引的属性，以记住它们绑定到的点。 

这些控件使用其自己的自定义位置处理程序：polygonPositionHandler。该函数查看控件的pointIndex并返回该特定点的当前画布位置。这样，交互和渲染就完成了。 

为了使实际控件更改当前点，我们需要编写一个自定义动作处理程序。 

更改点的位置实际上很容易：fabricObject.points [index] .x = number; fabricObject.points [index] .y =数字；困难的部分是在保持正确位置的同时处理改变尺寸的物体。 

我们需要一个锚点。我们选择将多边形位置固定在点数组的任意点的实际位置上，该点不是我们要拖动的点。

因此选择了点，我们就可以计算出它的实际绝对位置：

```js
var absolutePoint = fabric.util.transformPoint({
    x: (fabricObject.points[anchorIndex].x - fabricObject.pathOffset.x),
    y: (fabricObject.points[anchorIndex].y - fabricObject.pathOffset.y),
}, fabricObject.calcTransformMatrix());
```

修改多边形后，我们将使用该绝对位置。 

然后，将拖动的点替换为新的拖动点，重新计算多边形的width/height和pathOffset（基本上是重新初始化其尺寸）。 

现在要保持其位置稳定，我们想知道代表锚点的点，相对于多边形大小，该点现在处于哪个位置。

```js
var newX = (fabricObject.points[anchorIndex].x - fabricObject.pathOffset.x) / fabricObject.width,
    newY = (fabricObject.points[anchorIndex].y - fabricObject.pathOffset.y) / fabricObject.height;
```

现在newX和newY表示X和Y的点位置，范围从-0.5到0.5。Fabric支持范围从0到1的对象的数字原点。这让我们使用相对位置作为原点来转换旧的absolutePoint我们之前找到了。

```js
fabricObject.setPositionByOrigin(absolutePoint, newX + 0.5, newY + 0.5);
```

