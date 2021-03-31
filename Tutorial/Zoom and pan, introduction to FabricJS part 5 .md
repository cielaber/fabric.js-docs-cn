# Zoom and pan, introduction to FabricJS part 5

在上一个系列中，我们讨论了很多主题。从基本的对象操作到动画，事件，过滤器，组和子类。但是仍然有一些非常有趣和有用的事情要讨论！

### Zoom and panning(缩放和平移)

让我们看看如何通过鼠标交互来实现缩放和平移的基本系统。我们将使用鼠标滚轮在画布上放大20倍（2000％），并使用alt +单击动作进行拖动。
我们开始连接基本控件：

```js
canvas.on('mouse:wheel', function(opt) {
  var delta = opt.e.deltaY;
  var zoom = canvas.getZoom();
  zoom *= 0.999 ** delta;
  if (zoom > 20) zoom = 20;
  if (zoom < 0.01) zoom = 0.01;
  canvas.setZoom(zoom);
  opt.e.preventDefault();
  opt.e.stopPropagation();
})
```

这是基本的缩放控件，限制在1％到2000％之间。我们现在想添加画布的拖动。我们将使用ALT + DRAG，但是您可以更改为其他组合。这个想法是，将鼠标按下alt会将布尔值设置为true，这样鼠标移动事件便可以理解是拖动的时间了。

```js
canvas.on('mouse:down', function(opt) {
  var evt = opt.e;
  if (evt.altKey === true) {
    this.isDragging = true;
    this.selection = false;
    this.lastPosX = evt.clientX;
    this.lastPosY = evt.clientY;
  }
});
canvas.on('mouse:move', function(opt) {
  if (this.isDragging) {
    var e = opt.e;
    var vpt = this.viewportTransform;
    vpt[4] += e.clientX - this.lastPosX;
    vpt[5] += e.clientY - this.lastPosY;
    this.requestRenderAll();
    this.lastPosX = e.clientX;
    this.lastPosY = e.clientY;
  }
});
canvas.on('mouse:up', function(opt) {
  // on mouse up we want to recalculate new interaction
  // for all objects, so we call setViewportTransform
  this.setViewportTransform(this.viewportTransform);
  this.isDragging = false;
  this.selection = true;
});
    
```

好的，这是一个基本设置，可让您控制缩放和平移。仍然有一些可能的增强。
例如，我们可以进行滚轮缩放以使画布围绕光标所在的点居中：

```js
canvas.on('mouse:wheel', function(opt) {
  var delta = opt.e.deltaY;
  var zoom = canvas.getZoom();
  zoom *= 0.999 ** delta;
  if (zoom > 20) zoom = 20;
  if (zoom < 0.01) zoom = 0.01;
  canvas.zoomToPoint({ x: opt.e.offsetX, y: opt.e.offsetY }, zoom);
  opt.e.preventDefault();
  opt.e.stopPropagation();
});
    
```

最后，我们可以限制平移区域，以避免视图在一个方向上无限远。我们描画了代表我们平移区域的1000x1000像素矩形。并且我们添加了代码以限制该边界内的运动：

```js
canvas.on('mouse:wheel', function(opt) {
  var delta = opt.e.deltaY;//在向下滚动时返回正值，向上滚动时返回负值，否则为0。
  var zoom = canvas.getZoom();
  zoom *= 0.999 ** delta;
  if (zoom > 20) zoom = 20;
  if (zoom < 0.01) zoom = 0.01;
  canvas.zoomToPoint({ x: opt.e.offsetX, y: opt.e.offsetY }, zoom);
  opt.e.preventDefault();
  opt.e.stopPropagation();
  var vpt = this.viewportTransform;
  if (zoom < 400 / 1000) {
    vpt[4] = 200 - 1000 * zoom / 2;
    vpt[5] = 200 - 1000 * zoom / 2;
  } else {
    if (vpt[4] >= 0) {
      vpt[4] = 0;
    } else if (vpt[4] < canvas.getWidth() - 1000 * zoom) {
      vpt[4] = canvas.getWidth() - 1000 * zoom;
    }
    if (vpt[5] >= 0) {
      vpt[5] = 0;
    } else if (vpt[5] < canvas.getHeight() - 1000 * zoom) {
      vpt[5] = canvas.getHeight() - 1000 * zoom;
    }
  })
```



