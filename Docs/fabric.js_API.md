# fabric.js-API

## Classes

### Polyon-多边形

#### [cornerStyle :String](http://fabricjs.com/docs/fabric.Polygon.html#cornerStyle)

指定控件样式，“rect”或“circle”

##### Type:

- String



#### [cornerColor :String](http://fabricjs.com/docs/fabric.Polygon.html#cornerColor)

对象的控制角的颜色（当它处于活动状态时）

##### Type:

- String



#### [strokeUniform :Boolean](http://fabricjs.com/docs/fabric.Polygon.html#strokeUniform)

如果为“ false”，则stroke宽度将与对象成比例。设为true时，stroke将始终与为stroke宽度输入的确切像素大小匹配。默认为假

##### Type:

- Boolean



#### [getCenterPoint() → {](http://fabricjs.com/docs/fabric.Polygon.html#getCenterPoint)[fabric.Point](http://fabricjs.com/docs/fabric.Point.html)}

返回对象的实际中心坐标。实时获取。

##### Returns:

- Type

  [fabric.Point](http://fabricjs.com/docs/fabric.Point.html)



#### [getBoundingRect(absoluteopt, calculateopt) → {Object}](http://fabricjs.com/docs/fabric.Polygon.html#getBoundingRect)

返回对象的边界矩形（左、上、宽、高）的坐标。框与画布的轴对齐。实时获取。

| Name        | Type    | Attributes | Description                                                  |
| :---------- | :------ | :--------- | :----------------------------------------------------------- |
| `absolute`  | Boolean | <optional> | use coordinates without viewportTransform（使用不带viewportTransform的坐标） |
| `calculate` | Boolean | <optional> | use coordinates of current position instead of .oCoords / .aCoords（使用当前位置的坐标，而不是.oCoords.aCoords） |

##### Returns:

Object with left, top, width, height properties

- Type

  Object





### Canvas

#### [isDrawingMode :Boolean](http://fabricjs.com/docs/fabric.Canvas.html#isDrawingMode)

如果为true，则画布上的鼠标事件（mousedown / mousemove / mouseup）会导致自由绘制。按下鼠标后，mousemove将创建一个形状，然后将其进行最终确定，然后在画布上添加一个“ fabric.Path”实例。

##### Type:

- Boolean

```js
var canvas = new fabric.Canvas("canvas",{
  isDrawingMode:true
})
```







#### [preserveObjectStacking :Boolean](http://fabricjs.com/docs/fabric.Canvas.html#preserveObjectStacking)

指示对象在被选择时是否应保留在当前堆栈位置。将虚假对象置于顶部并呈现为选择组的一部分时

##### Type:

- Boolean

##### Examples

```js
var canvas = new fabric.Canvas('canvas',{
  preserveObjectStacking: true
});
```





#### [getActiveObjects() → {](http://fabricjs.com/docs/fabric.Canvas.html#getActiveObjects)[fabric.Object](http://fabricjs.com/docs/fabric.Object.html)}

返回当前所选对象的数组



#### [getActiveObject() → {](http://fabricjs.com/docs/fabric.Canvas.html#getActiveObject)[fabric.Object](http://fabricjs.com/docs/fabric.Object.html)}

返回当前活动对象



#### [getObjects(typeopt) → {Array}](http://fabricjs.com/docs/fabric.Canvas.html#getObjects)

返回此实例类型参数的子对象数组，该对象在1.3.10中引入，因为2.3.5以来，此方法始终返回该数组的COPY；否则，返回该数组的COPY值。



#### [setActiveObject(object, eopt) → {](http://fabricjs.com/docs/fabric.Canvas.html#setActiveObject)[fabric.Canvas](http://fabricjs.com/docs/fabric.Canvas.html)}

将给定对象设置为画布上唯一的活动对象

| Name     | Type                                                         | Attributes | Description                                        |
| :------- | :----------------------------------------------------------- | :--------- | :------------------------------------------------- |
| `object` | [fabric.Object](http://fabricjs.com/docs/fabric.Object.html) |            | Object to set as an active one                     |
| `e`      | Event                                                        | <optional> | Event (passed along when firing "object:selected") |



#### [discardActiveObject(e) → {](http://fabricjs.com/docs/fabric.Canvas.html#discardActiveObject)[fabric.Canvas](http://fabricjs.com/docs/fabric.Canvas.html)}

丢弃当前活动的对象和触发事件。如果构造函数是由于鼠标事件而由Fabric调用的，则该事件将作为参数传递并发送到触发函数以用于自定义事件。当用作方法时，e参数没有任何应用程序。

| Name | Type  | Description |
| :--- | :---- | :---------- |
| `e`  | event |             |

##### Returns:

thisArg

- Type

  [fabric.Canvas](http://fabricjs.com/docs/fabric.Canvas.html)



#### [toDataURL(optionsopt) → {String}](http://fabricjs.com/docs/fabric.Canvas.html#toDataURL)

将canvas元素导出到dataurl映像。注意，当使用倍增时，裁剪将适当缩放。

##### Parameters:

| Name      | Type   | Attributes | Description                           |
| :-------- | :----- | :--------- | :------------------------------------ |
| `options` | Object | <optional> | Options object  Properties:见如下表格 |

| Name                  | Type    | Attributes | Default | Description                                               |
| :-------------------- | :------ | :--------- | :------ | :-------------------------------------------------------- |
| `format`              | String  | <optional> | png     | The format of the output image. Either "jpeg" or "png"    |
| `quality`             | Number  | <optional> | 1       | Quality level (0..1). Only used for jpeg.                 |
| `multiplier`          | Number  | <optional> | 1       | Multiplier to scale by, to have consistent                |
| `left`                | Number  | <optional> |         | Cropping left offset. Introduced in v1.2.14               |
| `top`                 | Number  | <optional> |         | Cropping top offset. Introduced in v1.2.14                |
| `width`               | Number  | <optional> |         | Cropping width. Introduced in v1.2.14                     |
| `height`              | Number  | <optional> |         | Cropping height. Introduced in v1.2.14                    |
| `enableRetinaScaling` | Boolean | <optional> |         | Enable retina scaling for clone image. Introduce in 2.0.0 |

#####  Returns:

Returns a data: URL containing a representation of the object in the format specified by options.format

- Type

  String

##### Examples

Generate jpeg dataURL with lower quality(生成质量较低的jpeg数据URL)

```
var dataURL = canvas.toDataURL({
  format: 'jpeg',
  quality: 0.8
});
```

Generate cropped png dataURL (clipping of canvas)(生成裁剪的png dataURL（画布的裁剪）)

```
var dataURL = canvas.toDataURL({
  format: 'png',
  left: 100,
  top: 100,
  width: 200,
  height: 200
});
```

Generate double scaled png dataURL(生成双倍比例的png dataURL)

```
var dataURL = canvas.toDataURL({
  format: 'png',
  multiplier: 2
});
```









#### [getZoom() → {Number}](http://fabricjs.com/docs/fabric.Canvas.html#getZoom)

返回画布缩放级别

##### Returns:

- Type

  Number



#### [zoomToPoint(point, value) → {](http://fabricjs.com/docs/fabric.Canvas.html#zoomToPoint)[fabric.Canvas](http://fabricjs.com/docs/fabric.Canvas.html)}

设置此画布实例的缩放级别，缩放以点为中心，这意味着跟随缩放到具有相同点的点将具有从该点开始的缩放的视觉效果。要点不会动。它与视口的画布中心或视觉中心无关。

##### Parameters:

| Name    | Type                                                       | Description                           |
| :------ | :--------------------------------------------------------- | :------------------------------------ |
| `point` | [fabric.Point](http://fabricjs.com/docs/fabric.Point.html) | to zoom with respect to               |
| `value` | Number                                                     | to set zoom to, less than 1 zooms out |

##### Returns:

instance

- Type

  [fabric.Canvas](http://fabricjs.com/docs/fabric.Canvas.html)





### Object



#### [aCoords](http://fabricjs.com/docs/fabric.Object.html#aCoords)

在画布对象中描述对象的角位置绝对坐标属性为tl，tr，bl，br，并描述四个主角。每个属性都是一个带有x，y，Fabric.Point实例的对象。坐标取决于以下属性：宽度，高度，scaleX，scaleY skewX，skewY，角度，strokeWidth，顶部，左侧。这些坐标对于了解对象在哪里很有用。它们会使用oCoords更新，但是在缩放或平移更改时**不实时更新**。坐标使用@method setCoords更新。您可以计算它们而无需使用@method calcACoords（）更新。



#### [_getCoords(absolute) → {Object}](http://fabricjs.com/docs/fabric.Group.html#_getCoords)

返回相交的正确坐标集，这将返回aCoords或lineCoords。实时获取。

| Name       | Type    | Description                               |
| :--------- | :------ | :---------------------------------------- |
| `absolute` | Boolean | will return aCoords if true or lineCoords |

##### Returns:

{tl, tr, br, bl} points

- Type

  Object



#### [calcTransformMatrix(skipGroupopt) → {Array}](http://fabricjs.com/docs/fabric.Object.html#calcTransformMatrix)

根据对象的属性计算表示当前变换的变换矩阵。

| Name        | Type    | Attributes | Description                                                  |
| :---------- | :------ | :--------- | :----------------------------------------------------------- |
| `skipGroup` | Boolean | <optional> | return transform matrix for object not counting parent transformations There are some situation in which this is useful to avoid the fake rotation. |

##### Returns:

对象的变换矩阵

- Type

  Array



#### [hasBorders :Boolean](http://fabricjs.com/docs/fabric.Object.html#hasBorders)

设置为“ false”时，不呈现对象的控制边界

##### Type:

- Boolean



#### [hasControls :Boolean](http://fabricjs.com/docs/fabric.Object.html#hasControls)

设置为false时，不显示对象的控件，并且不能用于操作对象

##### Type:

- Boolean



#### [lockMovementX :Boolean](http://fabricjs.com/docs/fabric.Object.html#lockMovementX)

为“ true”时，对象水平移动被锁定

##### Type:

- Boolean



#### [lockMovementY :Boolean](http://fabricjs.com/docs/fabric.Object.html#lockMovementY)

为“ true”时，对象垂直运动被锁定

##### Type:

- Boolean





#### [lockRotation :Boolean](http://fabricjs.com/docs/fabric.Object.html#lockRotation)

当为true时，对象旋转被锁定

##### Type:

- Boolean



#### [bringForward(intersectingopt) → {](http://fabricjs.com/docs/fabric.Group.html#bringForward)[fabric.Object](http://fabricjs.com/docs/fabric.Object.html)}

在绘制的对象堆栈中向上移动对象。提升层级。

| Name           | Type    | Attributes | Description                                                  |
| :------------- | :------ | :--------- | :----------------------------------------------------------- |
| `intersecting` | Boolean | <optional> | If `true`, send object in front of next upper intersecting object |

##### Returns:

thisArg

- Type

  [fabric.Object](http://fabricjs.com/docs/fabric.Object.html)



#### [bringToFront() → {](http://fabricjs.com/docs/fabric.Group.html#bringToFront)[fabric.Object](http://fabricjs.com/docs/fabric.Object.html)}

将对象移动到绘制对象堆栈的顶部

##### Returns:

thisArg

- Type

  [fabric.Object](http://fabricjs.com/docs/fabric.Object.html)



#### [transparentCorners :Boolean](http://fabricjs.com/docs/fabric.Object.html#transparentCorners)

设置为true时，对象的控制角在内部呈现为透明（即stroke而不是填充）

##### Type:

- Boolean

## Namespaces

### utils

#### [(static) multiplyTransformMatrices(a, b, is2x2) → {Array}](http://fabricjs.com/docs/fabric.util.html#.multiplyTransformMatrices)

用矩阵A乘矩阵B来嵌套变换

| Name    | Type    | Description                               |
| :------ | :------ | :---------------------------------------- |
| `a`     | Array   | First transformMatrix                     |
| `b`     | Array   | Second transformMatrix                    |
| `is2x2` | Boolean | flag to multiply matrices as 2x2 matrices |

##### Returns:

两个变换矩阵的乘积

- Type

  Array



#### [(static) transformPoint(p, t, ignoreOffsetopt) → {](http://fabricjs.com/docs/fabric.util.html#.transformPoint)[fabric.Point](http://fabricjs.com/docs/fabric.Point.html)}

将变换t应用于点p

| Name           | Type                                                       | Attributes | Description                                     |
| :------------- | :--------------------------------------------------------- | :--------- | :---------------------------------------------- |
| `p`            | [fabric.Point](http://fabricjs.com/docs/fabric.Point.html) |            | The point to transform                          |
| `t`            | Array                                                      |            | The transform                                   |
| `ignoreOffset` | Boolean                                                    | <optional> | Indicates that the offset should not be applied |

##### Returns:

变换点

- Type

  [fabric.Point](http://fabricjs.com/docs/fabric.Point.html)