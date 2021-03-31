# Fabric.js Object caching(Fabric.js对象缓存)

### How does it work?(它是如何工作的？)

当fabric对象缓存处于活动状态时，在画布上绘制的对象实际上是在另一个较小的屏幕外画布上预先绘制的，与对象像素维度本身一样大。在“render”方法中，通过“drawImage”操作将预绘制的画布复制到主画布上。

这意味着在`drag`-`rotate`-`skew`-`scale`操作期间，对象不会在画布上重新绘制，而只是将其复制的缓存图像绘制在画布上。

### How can I tweak/customize it?(我如何调整/自定义它？)

此功能具有3个属性，可以不同方式使用它：

```js
/**
 * 当为true时，对象被缓存在另一个画布上。
 * 默认为 true
 * since 1.7.0
 * @type Boolean
 * @default
 */
objectCaching:            objectCaching,

/**
 * 当为true时，检查对象属性的高速缓存无效。 在某些特定情况下，您可能希望禁用此功能（喷刷，很大的路径组，组），或者如果您的应用程序不允许您修改组的属性，而您想对组禁用它。
 * 默认为 false
 * since 1.7.0
 * @type Boolean
 * @default
 */
statefullCache:            false,

/**
 * 如果为true，则在缩放过程中不会更新缓存。 如果缩放比例过大，图片将变得块状，并在缩放结束时以正确的细节重新绘制。此设置取决于性能和应用程序。
 * 默认为 false
 * since 1.7.0
 * @type Boolean
 * @default
 */
noScaleCache:              true,

/**
 * List of properties to consider when checking if cache needs refresh
 * Those properties are checked by statefullCache ON ( or lazy mode if we want ) or from single
 * calls to Object.set(key, value). If the key is in this list, the object is marked as dirty
 * and refreshed at the next render
 * @type Array
 */
cacheProperties: (
  'fill stroke strokeWidth strokeDashArray width height stroke strokeWidth strokeDashArray' +
  ' strokeLineCap strokeLineJoin strokeMiterLimit fillRule backgroundColor'
).split(' '),

/**
 * 当设置为“ true”时，对象的缓存将在下一次渲染调用时重新渲染。
 * @type Boolean
 * @default true
 */
dirty:                true,

/**
  * 当返回true时，强制对象拥有自己的缓存，即使它在组内，当对象在缓存上以特定方式运行并且始终需要其自己的隔离画布才能正确呈现时，也可能需要该对象。
  * since 1.7.12
  * @type function
  * @return false
  */
 needsItsOwnCache: function() {
   return false;
 },

 /**
 * 缓存画布的像素限制。 1Mpx，4Mpx应该可以。
 * @since 1.7.14
 * @type Number
 * @default
 */
fabric.perfLimitSizeTotal = 2097152;

/**
 * 缓存画布宽度或高度的像素限制。IE将最大值固定在5000
 * @since 1.7.14
 * @type Number
 * @default
 */
fabric.maxCacheSideLimit = 4096;

/**
 * *缓存画布的最低像素限制，设置为256PX
 * @since 1.7.14
 * @type Number
 * @default
 */
fabric.minCacheSideLimit = 256;
```

`objectCaching`是主要属性，浏览器默认为true，节点默认为false，它在对象级别启用objectCaching。

`statefullCache`此属性规则决定，Fabric是否应该自动检查，是否需要重新绘制缓存的副本，或者开发人员应该手动使它无效。此属性默认为false。稍后对此进行更多讨论。

`noScaleCache`默认为true，禁用缩放操作的缓存再生。它可以用来避免大比例的模糊效果。

`cacheProperties`每次Object.set调用（key，value），则在该属性数组中搜索key。如果找到它，将对象标记为需要重新渲染。StatefullCache设置为true，而是在每次渲染时将所有键与旧值的副本进行比较。

`dirty`是一个简单的标志，它强制在下一个渲染方法中重新渲染缓存，并在缓存重新生成后自动设置为false。

`fabric.perfLimitSizeTotal`生成的缓存画布的最大像素大小。

`fabric.maxCacheSideLimit`缓存画布最大边的最大大小（以像素为单位）。（大于5000的数字会中断IE）

`fabric.minCacheSideLimit`缓存画布最小侧的最大大小（以像素为单位）。（小于256的数字可以禁用gpu合成。待核实）

大于绘制对象的缓存画布的示例（默认情况下最小为256x256）：

<img class="show-borders" style="border:1px solid black" src="http://fabricjs.com/article_assets/carMinCache.png">

默认值（2兆像素）下最大缓存画布的示例。滚动查看内容：

<iframe class="show-borders" width="256" height="256" style="border:1px solid black" src="http://fabricjs.com/article_assets/carMaxCache.png">
	<html style="height: 100%;"><head><meta name="viewport" content="width=device-width, minimum-scale=0.1"><title>carMaxCache.png (1773×1182)</title></head><body style="margin: 0px; height: 100%"><img style="-webkit-user-select: none;" src="http://fabricjs.com/article_assets/carMaxCache.png"></body></html>
</iframe>

### How much performance gain I get, are there any problems?(我获得了多少性能，有没有什么问题？)

这取决于您的项目外观。您是否仅绘制了一堆圆，矩形和简单的多边形？也许您不会获得那么多的性能。 

您要导入并显示大型和复杂的svg吗？好吧，您将从可能的关闭变为平滑。我应该注意什么吗？好吧，您可能不喜欢`noScaleCache`功能，这就是为什么有一个标记将其禁用的原因。

### What about compatibility problem with my current project? Should I update? Can I disable caching?(与我当前项目的兼容性问题如何？我应该更新吗？我可以禁用缓存吗？)

我会说是的，切换到1.7.0，检查是否一切正常。如果没有向问题跟踪器报告任何视觉问题，您仍然可以在以下位置完全禁用该功能：

```js
fabric.Object.prototype.objectCaching = false;
```

覆盖标准值，并且将为您的项目禁用缓存。

### Live samples(活样品)

下面你可以看到2个fabric画布。左边的是默认的缓存，而右边的是绘制时禁用缓存，就像以前的版本一样。

画布上装有沉重的路径组，白雪皑皑，我能找到的最重的路径是3份副本，使渲染速度下降。尝试在左侧或右侧画布上拖动形状之一，注意速度差异。

<iframe>
  <html style="height: 100%;"><head><meta name="viewport" content="width=device-width, minimum-scale=0.1"><title>Live samples</title></head><body><div class="canvas-container" style="width: 450px; height: 400px; position: relative; user-select: none;"><canvas id="normal1" width="900" height="800" class="lower-canvas" style="position: absolute; width: 450px; height: 400px; left: 0px; top: 0px; touch-action: none; user-select: none;"></canvas><canvas class="upper-canvas " width="900" height="800" style="position: absolute; width: 450px; height: 400px; left: 0px; top: 0px; touch-action: none; user-select: none; cursor: default;"></canvas></div>
<div class="canvas-container" style="width: 450px; height: 400px; position: relative; user-select: none;"><canvas id="nocache1" width="900" height="800" class="lower-canvas" style="position: absolute; width: 450px; height: 400px; left: 0px; top: 0px; touch-action: none; user-select: none;"></canvas><canvas class="upper-canvas " width="900" height="800" style="position: absolute; width: 450px; height: 400px; left: 0px; top: 0px; touch-action: none; user-select: none; cursor: default;"></canvas></div> <script type="text/javascript">
var canvas1 = new fabric.Canvas('normal1');
var canvas2 = new fabric.Canvas('nocache1');
fabric.loadSVGFromURL('http://fabricjs.com/assets/176.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  canvas1.add(svg1);
});
fabric.loadSVGFromURL(' http://fabricjs.com/assets/48.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  canvas1.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/48.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  canvas1.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/48.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  canvas1.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/171.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  svg1.scale(0.4);
  canvas1.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/176.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  svg1.objectCaching = false;
  canvas2.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/48.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  svg1.objectCaching = false;
  canvas2.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/48.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  svg1.objectCaching = false;
  canvas2.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/48.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  svg1.objectCaching = false;
  canvas2.add(svg1);
});
fabric.loadSVGFromURL('http://fabricjs.com/assets/171.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  svg1.objectCaching = false;
  svg1.scale(0.4);
  canvas2.add(svg1);
});
</script></body></html></iframe>

如果效果无法展示，可前往官网体验：http://fabricjs.com/fabric-object-caching

还可以发现值为true或false的noScaleCache之间的缩放差异。

在左画布下方的画布中，canvas为false，这意味着在缩放tasinformation期间不会重新生成对象。如果将对象缩放到原始大小的3倍以上，则会注意到模糊，然后在执行鼠标向上移动时，会立即使用新的缓存副本进行修复。你自己试试：

<iframe><html style="height: 100%;"><head><meta name="viewport" content="width=device-width, minimum-scale=0.1"><title>noScaleCache</title></head><body style="margin: 0px; height: 100%"><div class="canvas-container" style="width: 450px; height: 400px; position: relative; user-select: none;"><canvas id="normal2" width="900" height="800" class="lower-canvas" style="position: absolute; width: 450px; height: 400px; left: 0px; top: 0px; touch-action: none; user-select: none;"></canvas><canvas class="upper-canvas " width="900" height="800" style="position: absolute; width: 450px; height: 400px; left: 0px; top: 0px; touch-action: none; user-select: none; cursor: default;"></canvas></div><script type="text/javascript">
var canvas3 = new fabric.Canvas('normal2');
fabric.loadSVGFromURL('http://fabricjs.com/assets/171.svg', function(objects, options) {
  var svg1 = fabric.util.groupSVGElements(objects, options);
  svg1.scale(0.05);
  canvas3.add(svg1);
});
</script></body></html></iframe>

如果效果无法展示，可前往官网体验：http://fabricjs.com/fabric-object-caching

### When does the cache gets updated with a new version?(缓存何时更新为新版本？)

Fabric有一个硬编码触发器，用于在开发人员没有简单方法插入代码的内置函数期间更新缓存。

这些情况是：缩放、键入文本、画布全局缩放。

在所有其他情况下，开发人员正在更改某个对象的某些属性以检测并触发更改，因此他还将通过操纵dirty标志将该对象设置为`dirty`。使用object方法`set`可以为您实现这一点。因此，调用`object.set（'fill'，'red'）`不需要其他操作。如果由于某些原因您没有使用set方法（例如，在文本对象处于设置某些属性的情况下会触发昂贵的函数），则将使用该标志。

还有一种方法要求结构在渲染时检查属性更改。大多数情况下这并不昂贵，但我决定不使用它，因为在拥挤的情况下（例如喷刷或1000多个svg路径），它很昂贵。

组和路径组的处理方式如下：

当一个对象设置了一个属性时，该属性被选中，如果它在`cacheProperties`数组中，则该对象和组被设置为dirty。如果属性在`stateProperties`数组中，则只有组被设置为dirty。

### How does fabric check for changes in custom subclasses with custom properties?(fabric如何检查具有自定义属性的自定义子类中的更改？)

自定义子类化是我认为最强大的结构特性之一，并且对象缓存已经建立在这个思想中。因此，有一个定义为`cacheProperties`的数组，它包含属性列表，当属性`statefulcache`设置为true时，在每次渲染时都会检查这些属性。（默认为false）。

数组如下所示：

```js
cacheProperties: (
    'fill stroke strokeWidth strokeDashArray width height' +
    ' strokeLineCap strokeLineJoin strokeMiterLimit fillRule backgroundColor'
  ).split(' '),
```

在不同的sublcasses中获得更多的属性，例如rect添加`rx`和`ry`等。这些属性递归地检查，这意味着在每次检测到更改时，都会保存该属性的副本，并且在下一次渲染时会进行深入的比较。通常需要深入检查的属性是gradients、patterns、path array、strokeDash。如果应用程序根本不使用某些属性，则可以将它们从cacheProperties数组中删除，并使检查更快，或者可以添加影响渲染的自定义属性以检查这些属性。

### Gotchas(陷阱)

图像是对象边界框的截面：是。有一个不可见的画布可以保存对象的副本。此画布的大小取决于对象的宽度/高度。如果宽度和高度不适合我们要显示的对象，图像将被切断。除了禁用对象的缓存或修复问题之外，没有其他解决方案。

当前受影响的用例：

```js
- (mainly solved since 1.7.7) sometimes things can look blurry
- (mainly solved since 1.7.8) paths with wrongly parsed command sequence (very few)
- (mainly solved since 1.7.3) Text using custom fonts with very big ascender/descender and of wich canvas is not able to measure extension.
- Groups not initialized correctly ( created empty and not updated after using the .add method, please use addWithUpdate)
- Scaling events that reset scale and modify width/height, disable noScaleCache, set it to false
```

