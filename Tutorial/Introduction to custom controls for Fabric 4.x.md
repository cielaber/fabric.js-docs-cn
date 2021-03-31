## Fabric.js demos · Custom controls api(Fabric.js demos·自定义控件api)

# Custom controls api

这个api是针对FabricJS4.0的，api和文本目前处于测试阶段。为了使命名和选项尽可能简单，在发布之前可能会发生一些小的变化。

## Introduction

此api允许开发人员创建自己的自定义控件，而不必对标准fabricJS控件方法使用重写。这使得自定义支持，容易，并使更新到最新版本总是可能的。

FabricJS在内部使用api来定义标准控件集，这些控件集具有与版本3及更低版本中相同的功能和定制。

### How does it work.

现在fabric.Object prototype有一个控制集，控制集只是一个普通的js对象，它有一组键，每个键的值都是一个fabric.Control class。为简化标准控件集，键保留了旧名称tl、mt、tr、mr、br、mb、bl、ml和mtr。

不同的是，绘制控件的代码不再知道任何关于控件的信息，而是将循环到此控件集的键并绘制每个控件。

默认情况下，所有fabricJS对象共享相同的控件集，但textbox有不同的控件，可以更改宽度而不是缩放。

没有什么可以阻止您为每个子类创建不同的控件集，或者在每个地方都有相同的控件集，并根据当前渲染的对象使控件可见/隐藏。

### Structure of a control

control是定位函数、渲染函数、样式函数、可见性函数、actionHandler和一组其他属性的集合，这些属性从fabricJS以正确的顺序调用和使用。有些属性是可选的，取决于控件的实现。

让我们看看fabricJS控件是如何在库本身中定义的，以标准对象的控件mr为例：

```js
objectControls.mr = new fabric.Control({
  x: 0.5,
  y: 0,
  cursorStyleHandler: scaleSkewStyleHandler,
  actionHandler: scalingXOrSkewingY,
  getActionName: scaleOrSkewActionName,
});
```

该控件是完全可配置的，可以提供处理程序，也可以提供默认处理程序的值。

默认控件在此处定义：[标准控件定义](https://github.com/fabricjs/fabric.js/blob/master/src/mixins/default_controls.js)。

#### visible and getVisibility

在FabricJS 4.0之前，控件可以设置为不可见，可以忽略。现在控件可以存在，也可以不存在，但也可以是临时不可变的，具体取决于对象状态。

控件的默认getVisibility函数将返回标准的object_controlsVisibility属性，该属性由于兼容性的原因而被忽略，如果未设置，则返控件 .visible值。

控件通常在实例之间共享，因此将控件设置为.visible=false将对所有对象隐藏它，而将对象方法用于控件可见性将对每个对象隐藏。

#### actioname and getActionName

属性actionName给出了控件可能执行的操作的名称。这用于在操作期间触发事件，FabricJS也用于标识用户在进行一些额外的优化时正在做的事情。如果您正在编写自定义控件，并且想知道代码中其他地方的情况，则可以在此处使用此字符串并监听结果事件。

事件的旧命名约定仍然受到尊重。因此，缩放操作仍将触发事件缩放和object：scaling。但也会触发额外的事件scale或scaleX，因为这是动作名称。因此，在创建自定义动作处理程序时（如果需要），您可以选择是否在动作处理程序中以您想要的名称显式触发事件，还是让Fabric触发与动作名称相同的事件。

请注意，不要对正在使用的默认fabricJS事件进行namig操作。

如果控件根据某些外部状态运行多个操作，还可以提供自定义getActionName。

例如，旧的mr控件可以用于2个动作，缩放或倾斜，结果取决于修改器键。

#### angle

标准fabricJS函数中未使用角度，如果需要具有方向的控件，并且在此处希望将其复用为不同的方向来使用它，请在此处将其编写为一个选项，以使其可以查找控件的角度。

请勿将其用于其他用途，在将来的版本中，我们可能会决定使用它来旋转控件。

#### x and y

x和y代表默认positionHandler使用的位置。对于放置在对象边界框上的大多数控件而言，默认位置处理程序已经足够好。 x和y的值表示边界框的大小，包括填充。

对于整个边界框，这些值的范围从-0.5到0.5，但是如果您需要将它们放置在很远的地方，则可以进一步扩展。 {x：0.5，y：0}将使控件在边界框右侧的垂直中心对齐。

#### offsetX and offsetY

offsetX和offsetY可让您以屏幕像素为单位偏移控件位置。一个典型的示例是标准旋转控件，该控件从边界框偏移了大约30px。Offsets代替fabric3.x的rotationPointOffset，现在可以将其应用于所有控件。 rotationPointOffset消失了。

#### withConnection

布尔值，指示控件（如果是偏移）是否与具有线到位置点的边界框连接。只是一个图形选项，没有功能。

#### cursorStyle and cursorStyleHandler

cursorStyleHandler是一个函数，负责将鼠标悬停在控件上时显示的光标。 

此特定功能需要显示一个不允许的光标，或者显示一个对齐的用于偏斜或缩放的光标。该函数需要为您可以使用的浏览器返回一个有效的游标。内联url编码的SVG也是可能的。

```js
function changeWidth(eventData, transform, x, y) {
  var target = transform.target, localPoint = getLocalPoint(transform, transform.originX, transform.originY, x, y),
      strokePadding = target.strokeWidth / (target.strokeUniform ? target.scaleX : 1),
      newWidth = Math.abs(localPoint.x / target.scaleX) - strokePadding;
  target.set('width', Math.max(newWidth, 0));
  return true;
}
```

该函数获取鼠标事件数据，因此可以确定是否按下了某个热键，然后针对某个动作或其他动作运行特定逻辑。然后，skewCursorStyleHandler或scaleCursorStyleHandler将处理一个动作或另一个动作的特定逻辑。现有的fabricJS功能的所有样式处理均已编写，并作为示例，以防您需要编写其他游标逻辑。

如果您的自定义控件始终显示相同的光标，则可以保留默认处理程序，并通过设置cursorStyle属性来更改该控件。

#### mouseUpHandler and mouseDownHandler

如果需要，您可以将控件用作一次单击按钮，而不是将其连接以使用鼠标拖动动作。一个经典的示例是删除对象或克隆对象的控件，如本例中更好地描述的那样：[自定义控件渲染和操作](http://fabricjs.com/custom-control-render)。或另一种可能是翻转对象。

#### actionHandler

该功能绝对是掌握起来最复杂的功能，它使您可以编写自定义操作。我们可以看一下简单的宽度转换器功能：

```js
function changeWidth(eventData, transform, x, y) {
  var target = transform.target, localPoint = getLocalPoint(transform, transform.originX, transform.originY, x, y),
      strokePadding = target.strokeWidth / (target.strokeUniform ? target.scaleX : 1),
      newWidth = Math.abs(localPoint.x / target.scaleX) - strokePadding;
  target.set('width', Math.max(newWidth, 0));
  return true;
}
```

参数：eventData是鼠标事件对象，transform是一个普通的js对象，其中包含transform.target，我们要转换的对象x和y表示距转换锚点的像素距离。

此函数处理文本框的侧面控件，但也可以按原样使用在rects上，创建另一个用于更改高度的控件很简单，只需将与宽度相关的属性交换为与高度相关的属性即可。

另一个简单的扩展是使其更改椭圆的rx，ry或圆的半径。

进行缩放和倾斜计算会更加复杂。请注意，尽管api提供了完全的自由度，但为控件编写动作并不容易。当几何学发挥作用时，了解fabricJS内部结构非常重要。实际操作只是一个示例，但是由于必须支持，因此代码的方式变得更加复杂：scalingFlip，lockScaling，centerTransfrom，freeformScaling和其他fabricJS属性。

如果您认为部分代码可用于您自己的操作（例如wrapWithFixedAnchor），请打开一个issue，要求将其分开并将其放在自己的函数中。