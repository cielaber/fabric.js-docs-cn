# Using transformations, Introduction to Fabric.js Part 6

理解transformations如何在fabricJS上工作是尽可能顺利地编写应用程序的一个关键方面。

### Methods and properties related to transformations(与转换有关的方法和属性)

如果您计划了解和使用带有自定义代码的fabricJS转换，那么这些是您应该学会使用最多的方法。 一般而言，在此页面中，我们将矩阵称为6个数字的数组，这些数组表示平面上的变换，并指向一个简单的JS对象，看起来像{x：number，y：number}或fabric.Point类实例。 （通常没有什么区别）

```
Canvas:
- viewportTransform = matrix;
Objects:
- matrix = fabric.Object.prototype.calcTransformMatrix();
- matrix = fabric.Object.prototype.calcOwnMatrix();
Utils:
- point = fabric.util.transformPoint(point, matrix);
- matrix = fabric.util.multiplyTransformMatrices(matrix, matrix);
- matrix = fabric.util.invertTransform(matrix);
- options = fabric.util.qrDecompose(matrix);
```

### Moving from one space to another(从一个空间移动到另一个空间/空间变换)

使用fabricJS时，通常必须与坐标和位置进行交互，但是如果您没有正确的背景，那么了解这些坐标的位置可能会很麻烦。 我将列出转换及其用法，然后尝试制作2个示例，以更好地说明发生了什么以及如何执行。 

Canvas.viewportTransform：将虚拟画布的一个点移至缩放和平移空间。 

在应用从viewportTransfrom M表示的缩放和平移后，当画布未缩放和平移时，位于位置P的点可以在坐标处找到： `newP = fabric.util.transformPoint(P，canvas.viewportTransfor)`; 

Object.calcTransformMatrix：返回表示特定时刻（受top，left，scale和许多其他属性影响）通用对象变换的矩阵，并将点从对象空间移动到画布空间，而不缩放。因此，给定对象空间坐标中的一个点位于坐标P处，该点将在画布上的以下位置绘制： `newP = fabric.util.transformPoint(P，object.calcTransformMatrix())`;

### Transforms order(转换顺序)

在渲染期间，Fabric按以下顺序应用转换：

```
zoom and pan => object transformation => nested object ( group ) => additionally nested objects ( nested groups )
```

### Reverting order(还原顺序)

invertTransform实用程序用于移回转换逻辑，以便进行一些反向计算： 假设您要在单击的点上用鼠标单击将对象标记在画布上。您单击P点，例如在元素上10,10像素。您的对象被缩放和旋转，并且画布被缩放和平移。 

要反转渲染计算，您可以遵循以下逻辑：

```js
// calculate the total transformation that is applied to the objects pixels:
var mCanvas = canvas.viewportTransform;
var mObject = object.calcTransformMatrix();
var mTotal = fabric.util.multiplyTransformMatrices(mCanvas, mObject); // inverting the order gives wrong result
var mInverse = fabric.util.invertTransform(mTotal);
var pointInObjectPixels = fabric.util.transformPoint(pointClicked, mInverse);
```

现在，pointInObjectPixels是位于坐标空间中的点，其中(0,0)处位于对象的中心。

### Understanding the effect of a matrix(了解矩阵的效果)

给定top，left，angle，scaleX，scaleY，skewX，skewY，flipX，flipY，创建表示该转换的矩阵相对简单。 

不直接的是如何返回。矩阵有6个维度，有6个数字，而属性是7个，因为我们可以按比例吸收翻转。确实存在无限多个矩阵，但是属性的可能组合数目是一个无限大。 

这里是`Fabric.util.qrDecompose(matrix)`;可以为我们解码矩阵。给定函数的通用可逆矩阵，它将返回包含这些信息的选项对象：

```
{
  angle: number, // in degree
  scaleX: number,
  scaleY: number,
  skewX: number, // in degree
  skewY: 0, // always 0! in degree.
  translateX: number,
  translateY: number,
}
```

该函数返回此矩阵的一种可能的解决方案，将skewY约束为0。

### A real use case(一个真实的案例)

一位开发人员希望将对象分组在一起，但要同时保持它们空闲。理想情况下，当主要物体移动时，他希望其他物体跟随它。 

为了解释这个例子，我将调用主对象BOSS和其他MINIONS。 

因此，让我们想象一下在画布上有一些对象，我们可以自由移动它们。在某些时候，我们想锁定它们的相对位置并缩放在一起并移动一个。当我们设置所需的位置时，BOSS的位置将由矩阵来描述，正如我们到目前为止所学到的，每个minions也是如此。 

我确定它存在一个矩阵，该矩阵定义了从boss到minions的必要转换，我必须找到它。

```js
// i m looking for the UNKNOW relation matrix where:
BOSS * UNKNOW = MINION
// i multiply left for BOSS-INV
BOSS-INV * BOSS * UNKNOW = BOSS-INV * MINION
// BOSS-INV * BOSS = IDENTIY, a neutral matrix.
IDENTITY * UNKNOW = BOSS-INV * MINION
// so...
UNKNOW = BOSS-INV * MINION
// that in fabricJS code equals to:
var minions = canvas.getObjects().filter(o => o !== boss);
var bossTransform = boss.calcTransformMatrix();
var invertedBossTransform = fabric.util.invertTransform(bossTransform);
minions.forEach(o => {
  var desiredTransform = multiply(invertedBossTransform, o.calcTransformMatrix());
  // save the desired relation here.
  o.relationship = desiredTransform;
});
```

好了，现在我知道如何找到这种关系了，我可以编写一些事件处理程序以将这种关系应用于每个BOSS动作。

```js
var canvas = new fabric.Canvas('c');
var boss = new fabric.Rect(
  { width: 150, height: 200, fill: 'red' });
var minion1 = new fabric.Rect(
  { width: 40, height: 40, fill: 'blue' });
var minion2 = new fabric.Rect(
  { width: 40, height: 40, fill: 'blue' });

canvas.add(boss, minion1, minion2);

boss.on('moving', updateMinions);
boss.on('rotating', updateMinions);
boss.on('scaling', updateMinions);

var multiply = fabric.util.multiplyTransformMatrices;
var invert = fabric.util.invertTransform;

function updateMinions() {
  var minions = canvas.getObjects().filter(o => o !== boss);
  minions.forEach(o => {
    if (!o.relationship) {
      return;
    }
    var relationship = o.relationship;
    var newTransform = multiply(
      boss.calcTransformMatrix(),
      relationship
    );
    opt = fabric.util.qrDecompose(newTransform);
    o.set({
      flipX: false,
      flipY: false,
    });
    o.setPositionByOrigin(
      { x: opt.translateX, y: opt.translateY },
      'center',
      'center'
    );
    o.set(opt);
    o.setCoords();
  });
}

document.getElementById('bind').onclick = function() {
  var minions = canvas.getObjects().filter(o => o !== boss);
  var bossTransform = boss.calcTransformMatrix();
  var invertedBossTransform = invert(bossTransform);
  minions.forEach(o => {
    var desiredTransform = multiply(
      invertedBossTransform,
      o.calcTransformMatrix()
    );
    // save the desired relation here.
    o.relationship = desiredTransform;
  });
}
```

[demo](http://fabricjs.com/using-transformations)

