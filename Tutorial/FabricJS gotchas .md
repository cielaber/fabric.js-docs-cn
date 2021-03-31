# FabricJS gotchas

这个页面包含了第一次接触fabricJS的人打开的最常见问题的列表。这些缺陷的产生，既有解释不清的原因，也有文档不完善的原因。在这里，我们试图解决共同的问题。

### Objects are no more selectable - setCoords(对象不再是可选择的-setCoords)

Fabric包含两组坐标以快速知道物体在画布上的位置。它们链接到两个对象属性：oCoords和aCoords。

当用户与对象交互或结束变换（例如拖动）时，fabricJS会自动更新这些坐标。在所有其他情况下，开发人员必须调用`对象.setCoords（）`以便在渲染位置识别对象。

最常见的症状是对象不可选择。这发生在通过“top/left”或“scale”或“canvas”视口更改开发代码之后。在这些操作之后，相同的代码最终应该对所有对象调用“setCoords（）”。

```js
function repositionRect(x, y) {
    rect.left = x;
    rect.top = y;
    rect.setCoords();
  }
```

### Wrong position after reloading a JSON object - NUM_FRACTION_DIGITS(重新加载JSON对象后位置错误-NUM_FRACTION_DIGITS)

Fabric可以以纯对象格式序列化和反序列化对象。

在处理序列化时，float可能是一个问题，并提供带有不必要数量小数的长字符串。这会使字符串大小增大。

为了减少这种情况，在名为“NUM_ufracts\u DIGITS”的对象上定义了一个常量，历史上设置为2。这意味着，顶部值'3.454534413123'被保存为'3.45'，对于比例、宽度、高度相同。除非你在没有精度问题的情况下进行处理，否则这基本是最好的。

举一个例子，可以使用“ 0.0151”的比例将非常大的图像缩小为较小的尺寸。 

在这种情况下，序列化会将其另存为“ 0.02”，从而有意义地改变了比例。如果遇到这种情况，请在项目中设置更高的常量：`fabric.Object.NUM_FRACTION_DIGITS = 8`以使属性具有8位小数。 这也会影响SVG导出。

这也会影响SVG导出。

### Objects misbehave when dealing with text input - numbers vs strings(对象在处理文本输入时表现不正常-numbers vs strings)

有时，在原型和概念的快速证明中，人们使用文本输入来更改fabric对象的属性。

Fabric文档指出top，left，scaleX，angle和其他属性需要数字作为值。 

文本输入返回字符串。当将字符串转换为数字时，FabricJS不会检查类型也不进行转换，这是由于某些代码的副作用，而不是要依赖的功能。 

在将值分配给需要数字的属性之前，请使用parseInt和parseFloat。

### Object does not update after changing property - objectCaching(更改属性后对象不会更新-objectCaching)

造成混淆的常见原因是，开发人员分配了新的属性来填充并且对象在renderAll之后不更新。 FabricJS确实将对象缓存为图像以加快渲染速度。如果您想让fabricJS知道某些更改并且需要重绘特定对象，请使用set方法。

```js
rect.set('fill', 'red');
canvas.requestRenderAll();
```

或者，作为替代：

```js
rect.fill = 'red';
rect.stroke = 'blue';
rect.set('dirty', true);
```

有关更多信息和详细说明，请查看[专用的缓存页面](http://fabricjs.com/fabric-object-caching)。

### Objects have a transparent stroke of width 1 by default(默认情况下，对象的透明stroke宽度为1)

默认情况下，对象的宽度为1的透明stroke会在水平和垂直方向上将其移动0.5个像素。这将使您难以将对象定位在准确的位置。造成这种情况的原因有两个：-如果没有strokeWidth，则设置stroke color不会带来任何结果-SVG具有相同的默认值，因此对于svg导入来说，这样做是有道理的-在fabric v1.5之前，stroke不是控制边界框和位置的部分，所以这不是问题。要删除stroke：

```js
rect.set('strokeWidth', 0);
```

