# Introduction to Fabric.js. Part 4.

在上一个系列中，我们讨论了很多主题。从基本的对象操作到动画，事件，过滤器，组和子类。但是仍然有一些非常有趣和有用的事情要讨论！

### Free drawing

如果说有什么功能能让`<canvas>`在眼前一亮，那一定是它能够很好的支持自由绘图！由于画布只是一个2D位图， Fabric为我们提供了一张纸：可以自由绘画，而且非常自然。 

只需将Fabric画布的`isDrawingMode`属性设置为true即可启用免费绘图模式。这立即使在画布上的任何进一步的单击和移动都被解释为铅笔/画笔。 当`isDrawingMode`为true时，您可以根据需要在画布上绘制多次。但是，一旦执行任何移动，然后执行“ mouseup”事件，Fabric就会触发“ path:created”事件，并实际上将刚刚绘制的形状转换为真实的`fabric.Path`实例！ 

如果随时将`isDrawingMode`设置为false，最终将在画布上仍然存在所有创建的路径对象。而且，由于它们是很好的旧`fabric.Path`对象，因此您可以根据需要进行任意修改（移动，旋转，缩放等）。 

还有两个可用于自定义自由绘制的属性-`freeDrawingBrush.color`和`freeDrawingBrush.width`。两者都可以通过`freeDrawingBrush`实例在Fabric画布实例上使用。 `freeDrawingBrush.color`可以是任何常规颜色值，并表示画笔的颜色。 `freeDrawingBrush.width`是一个以像素为单位的数字，代表画笔的厚度。![4_1的副本](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/4_1.png)

在不久的将来，我们计划为免费绘画添加更多选项-各种版本的画笔（例如，类似喷雾或粉笔的刷子）。还有自定义画笔图案，以及可以自己扩展的选项，类似于Fabric图像滤镜。

### Customization

Fabric的令人惊奇的事情之一是它的可定制性。您可以调整canvas或canvas对象上的数十个各种参数，以使事物的行为完全符合您想要的方式。让我们来看看其中的一些。

#### Locking objects

画布上的每个对象都可以通过几种方式锁定。 “ lockMovementX”，“ lockMovementY”，“ lockRotation”，“ lockScalingX”，“ lockScalingY”是用于锁定相应对象动作的属性。因此，将`object.lockMovementX`设置为true可以防止对象水平移动。您仍然可以在垂直平面上移动它。同样，`lockRotation`防止旋转，并且`lockScalingX` /` lockScalingY `—缩放。所有这些都是累积性的。您可以通过任何方式将它们组合在一起。

#### Changing borders, corners

您可以通过“ hasControls”和“ hasBorders”属性控制对象的边界和角的可见性。只需将它们设置为false，对象立即呈现“裸”状态。

```vue
<template>
  <div class="groups">
    <canvas id="canvas"></canvas>
  </div>
</template>
<script>
import { fabric } from "fabric";
export default {
  data() {
    return {
      canvas: "",
    };
  },
  mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });
    var circle = new fabric.Circle({
        left:100,
        top:100,
        fill:"red",
        radius:50
    })

    circle.hasBorders = false;

    this.canvas.add(circle)

  },
};
</script>
<style>
</style>
```

![image-20210329162704784](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329162704784.png)

```js
circle.hasControls = false;
```

![image-20210329163601096](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329163601096.png)

您还可以通过调整一些自定义属性“ cornerDashArray”，“ borderDashArray”，“ borderColor”，“ transparentCorners”，“ cornerColor”，“ cornerStrokeColor”，“ cornerStyle”，“ selectionBackgroundColor”，“ padding”和“ cornerSize”来更改其外观。

```js
circle.set({
  borderColor:"red",
  cornerColor:"green",
  cornerSize:6
})
```

![image-20210329164124805](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329164124805.png)

```js
circle.set({
  transparentCorners:false,
  cornerColor:"blue",
  cornerStrokeColor:"red",
  borderColor:"red",
  cornerSize:12,
  padding:10,
  cornerStyle:"circle",
  borderDashArray:[3,3]
})
```

![image-20210329164531535](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329164531535.png)

#### Disabling selection

您可以通过将canvas的“ selection”属性设置为false来禁用画布上对象的选择。这样可以防止在画布上显示的所有内容上进行选择。如果只需要使某些对象不可选择，则可以更改对象的“ selectable”属性。只需将其设置为false，对象就会失去交互性。

#### Customizing selection

现在，如果您不想禁用选择，而是想更改其外观怎么办？没问题。 画布上有4个控制其显示的属性-“ selectionColor”，“ selectionBorderColor”，“ selectionLikeWidth”和“ selectionDashArray”。这些应该是不言自明的，所以让我们看一个例子：

```js
this.canvas.add(new fabric.Circle({
  radius:30,
  fill:"#f55",
  top:100,
  left:100
}))

this.canvas.selectionColor = 'rgba(0,255,0,0.3)'
this.canvas.selectionBorderColor = "red";
this.canvas.selectionLineWidth = 5;
```

![image-20210329165610756](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329165610756.png)

最后一个属性“ selectionDashArray”不是那么简单。它允许我们做的是使选择线变为虚线。定义虚线图案的方法是通过数组指定间隔。因此，要创建一个模式，即先有一个长破折号，然后是一个短破折号，我们可以将[10，5]之类的内容用作“ selectionDashArray”。这将画出10px长的线，然后跳过5px，再次画10px线，依此类推。如果要使用[2，4，6]数组，则将通过绘制2px线，然后跳过4px，然后绘制6px线，然后跳过2px，再绘制4px线，然后跳过6px，来创建图案。你明白了。例如，这是[2,4,6]模式的外观：

```js
this.canvas.add(new fabric.Circle({
  radius:30,
  fill:"#f55",
  top:100,
  left:100
}))

this.canvas.selectionColor = 'rgba(0,255,0,0.3)'
this.canvas.selectionBorderColor = "red";
this.canvas.selectionLineWidth = 5;
this.canvas.selectionDashArray = [2,4,6]
```

![image-20210329165701821](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329165701821.png)

#### Dashed stroke

与画布上的“ selectionDashArray”类似，所有Fabric对象都具有“ strokeDashArray”属性，该属性负责对对象执行的任何笔划以虚线表示。

```js
var rect = new fabric.Rect({
  fill:"#06538e",
  width:125,
  height:125,
  top:50,
  left:50,
  stroke:"red",
  strokeDashArray:[5,5]
})

this.canvas.add(rect);
```

![image-20210329170059821](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329170059821.png)

#### Clickable area

如您所知，所有的Fabric对象都有边界框，当存在控件/拐角时，该边界框用于拖动对象或旋转和缩放它。您可能已经注意到，即使单击对象边界框内没有绘制任何内容的空间，也可以拖动对象。

看一下这张图片：

![4_14](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/4_14.png)

默认情况下，可以通过边界框拖动画布上的所有Fabric对象。但是，如果您想要不同的行为-仅按对象的实际内容单击/拖动对象，则可以在对象上使用“ perPixelTargetFind”属性。只需将其设置为true即可获得所需的行为。

#### Rotating point

由于版本1.0 Fabric默认情况下使默认UI，因此无法再缩放和旋转对象。相反，每个对象上都有一个单独的旋转控件。该控件的相应属性是“ hasRotatingPoint”。您可以通过“ rotatingPointOffset”数字属性自定义其相对于对象的偏移量。

![4_10](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/4_10.png)

#### Object transformation

自1.0版以来，Fabric中还有许多其他与转换相关的属性。其中之一是canvas实例上的“ uniScaleTransform”。默认情况下为false，可用于启用对象的非均匀缩放；换句话说，它允许在拖动角落时更改对象的比例。

![4_15](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/4_15.png)

然后有“ centeredScaling”和“ centeredRotation”属性（在v1.3.4之前是一个属性，即“ centerTransform”）。它们指定是否应将对象的中心用作变换的原点。当它们都设置为true时，当对象总是从中心缩放/旋转时，它将复制1.0之前的行为。由于1.0的转换原点是动态的，因此在缩放对象时可以进行更精细的控制。 

最后一对新属性是“originX”和“originY”。默认情况下，将其相应地设置为“left”和“top”，它们允许以编程方式更改对象的变换原点。当您拖动对象的角时，正是这些属性在引擎盖下动态变化。 

那么我们什么时候手动更改它们？例如，使用文本对象时。当您动态更改文本，并且文本框的尺寸增加时，“ originX”和“ originY”将指出框的增长位置。因此，如果需要将文本对象居中，则可以将originX设置为“ center”。要将其粘贴在右侧，可以将originX设置为“ right”。等等。此行为类似于CSS中的“ position:absolute”。

#### Canvas background and overlay

您可能在第1部分中已经记起，您可以分配一种颜色来填充整个画布背景。只需将任何常规颜色值设置为画布的“ backgroundColor”属性即可。

```js
this.canvas.add(new fabric.Circle({
  radius:30,
  fill:"#f55",
  top:100,
  left:100
}))
this.canvas.backgroundColor = "rgba(0,0,255,0.3)";
this.canvas.renderAll();
```

![image-20210329172317013](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329172317013.png)

您甚至可以走得更远，并将图像指定为背景。为此，您需要使用setBackgroundImage方法，并传递url和optiona补全回调。

```js
this.canvas.add(new fabric.Circle({
  radius:30,
  fill:"#f55",
  top:100,
  left:100
}))

this.canvas.setBackgroundImage("https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/aa18972bd40735fa8d2f0c739e510fb30e240818.jpeg",this.canvas.renderAll.bind(this.canvas))
//由于这里我使用本地图片无效，因此使用了网络图片
```

重要的是要注意，尽管该属性称为`backgroundImage`，但它可以承载任何结构对象类型。您可以设置一个“ fabric.Rect”来代表一个画板，或者可以设置一组对象。下面的“ overlayImage”或可以托管任何填充物（例如渐变或图案）的“ backgroundColor”也是如此。

![image-20210329173310625](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329173310625.png)

最后，您还可以设置叠加图像，在这种情况下，叠加图像将始终显示在画布上渲染的所有对象的顶部。只需使用setOverlayImage，传递url和可选的完成回调即可。





### Fabric on Node.js

Fabric的独特之处之一是它不仅可以在客户端，浏览器中而且可以在服务器上工作！当您要从客户端发送数据并直接在服务器上创建该数据的映像时，这可能会很有用。或者，出于速度，方便或其他原因，如果您只是想从控制台使用Fabric API。 

让我们看一下如何设置Node环境并启动Fabric。 

首先，如果尚未安装，则需要安装[Node.js](http://nodejs.org/)。根据平台的不同，安装节点的方法很少。您可以按照以下[说明](http://howtonode.org/how-to-install-nodejs)进行操作，或者[这里](https://github.com/joyent/node/wiki/Installation)。 

安装Node之后，我们需要安装[node-canvas](https://github.com/LearnBoost/node-canvas)库。 node-canvas是NodeJS的Canvas实现。它依靠[Cairo](http://cairographics.org/) —可以在Mac，Linux或Windows上运行的2D图形库。根据您选择的平台，node-canvas具有专用的[安装说明](https://github.com/LearnBoost/node-canvas/wiki)。 由于Fabric在Node之上运行，因此它是NPM软件包。因此，下一步是安装NPM。您可以在其[github存储库](https://github.com/isaacs/npm)中找到安装说明。 

最后一步是使用NPM安装[Fabric软件包](https://npmjs.org/package/fabric)。只需运行`npm install fabric`（或`npm install -g fabric`以全局安装软件包）即可完成此操作。 

如果现在运行节点控制台，则应该同时使用node-canvas和Fabric：

```bash
> node
...
> typeof require('canvas'); // "function"
> typeof require('fabric'); // "object"
```

现在一切就绪，我们可以尝试一个简单的“ hello world”测试。让我们创建一个helloworld.js文件：

```js
var fs = require('fs'),
    fabric = require('fabric').fabric,
    out = fs.createWriteStream(__dirname + '/helloworld.png');

var canvas = new fabric.StaticCanvas(null, { width: 200, height: 200 });
var text = new fabric.Text('Hello world', {
  left: 100,
  top: 100,
  fill: '#f55',
  angle: 15
});
canvas.add(text);
canvas.renderAll();

var stream = canvas.createPNGStream();
stream.on('data', function(chunk) {
  out.write(chunk);
});
```

然后将其作为节点helloworld.js运行。打开helloworld.png揭示了这一点：

![4_11](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/4_11.png)

那么这是怎么回事？让我们来看一下这段代码的重要部分。 

首先，我们包括了Fabric本身（`fabric = require('fabric')fabric`）。然后，我们创建好旧的Fabric画布。 然后是看起来很熟悉的对象创建（`new fabric.Text()`）和画布添加文本对象（`canvas.add(text)`）。 所有这些简单地创建了Fabric画布并将文本对象呈现到画布上。现在，如何创建画布上渲染的图像？使用`createPNGStream`方法可以在画布实例上使用。 `createPNGStream`返回Node的[流对象](http://nodejs.org/api/stream.html)，然后可以使用`on('data')`将其输出到图像文件中，并写入与图像文件相对应的流中`fs.createWriteStream()`。 

`fabric.Canvas＃createPNGStream`是特定于Node的方法之一。其他所有工作原理相同—您仍然可以像平常一样创建对象，将其添加到画布上，进行修改，渲染等。

#### Node server and Fabric

作为示例，让我们创建一个简单的Node服务器，该服务器将侦听JSON格式的Fabric数据的传入请求，并输出该数据的图像。整个脚本只有25行！

```js
var fabric = require('fabric').fabric, // or import { fabric } from 'fabric';
    http = require('http'),
    url = require('url'),
    PORT = 8124;

var server = http.createServer(function (request, response) {
  var params = url.parse(request.url, true);
  var canvas = new fabric.StaticCanvas(null, { width: 200, height: 200 });

  response.writeHead(200, { 'Content-Type': 'image/png' });

  canvas.loadFromJSON(params.query.data, function() {
    canvas.renderAll();

    var stream = canvas.createPNGStream();
    stream.on('data', function(chunk) {
      response.write(chunk);
    });
    stream.on('end', function() {
      response.end();
    });
  });
});

server.listen(PORT);
```

此代码段中的大多数代码应该已经很熟悉。其要点是服务器响应内部。我们正在创建Fabric画布，将JSON数据加载到该画布上，进行渲染，并将最终结果作为服务器响应进行流式传输。 为了对其进行测试，让我们为一个绿色的，稍微旋转的矩形获取数据：

```
{"objects":[{"type":"rect","left":103.85,"top":98.85,"width":50,"height":50,"fill":"#9ae759","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1.39,"scaleY":1.39,"angle":30,"flipX":false,"flipY":false,"opacity":0.8,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0}],"background":"rgba(0, 0, 0, 0)"}
```

URI编码：

```
%7B"objects"%3A%5B%7B"type"%3A"rect"%2C"left"%3A103.85%2C"top"%3A98.85%2C"width"%3A50%2C"height"%3A50%2C"fill"%3A"%239ae759"%2C"overlayFill"%3Anull%2C"stroke"%3Anull%2C"strokeWidth"%3A1%2C"strokeDashArray"%3Anull%2C"scaleX"%3A1.39%2C"scaleY"%3A1.39%2C"angle"%3A30%2C"flipX"%3Afalse%2C"flipY"%3Afalse%2C"opacity"%3A0.8%2C"selectable"%3Atrue%2C"hasControls"%3Atrue%2C"hasBorders"%3Atrue%2C"hasRotatingPoint"%3Afalse%2C"transparentCorners"%3Atrue%2C"perPixelTargetFind"%3Afalse%2C"rx"%3A0%2C"ry"%3A0%7D%5D%2C"background"%3A"rgba(0%2C%200%2C%200%2C%200)"%7D
```

并通过“data”查询参数传递给服务器。立即响应，返回“ image / png”内容类型，如下所示：

![4_12](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/4_12.png)

如您所见，在服务器上使用Fabric是非常容易和直接的。随时尝试使用此代码段。也可以从URL参数中更改画布尺寸，或者在返回图像作为响应之前修改客户端数据。

#### Custom fonts in Fabric on Node

在Fabric中使用自定义字体之前，我们需要先加载它们。在浏览器（客户端）中，最常见的加载字体的方法是使用[CSS3 @ font-face](http://www.w3.org/TR/css3-fonts/#font-face-rule)规则。在节点上的Fabric（服务器端）中，我们可以利用node-canvas字体API，该API使在公园中散步加载字体成为可能。 

下面的示例演示如何加载和使用自定义字体。将其保存到customfont.js，并确保字体文件的路径正确。在此示例中，我们使用[Ubuntu](http://www.google.com/fonts/specimen/Ubuntu)作为我们的自定义字体。

```js
 var fs = require('fs'),
      fabric = require('fabric').fabric; // or import { fabric } from 'fabric';

  fabric.nodeCanvas.registerFont(__dirname + '/test/fixtures/Ubuntu-Regular.ttf', {
    family: 'Ubuntu', weight: 'regular', style: 'normal'
  });
  fabric.nodeCanvas.registerFont(__dirname + '/test/fixtures/Ubuntu-Bold.ttf', {
    family: 'Ubuntu', weight: 'bold', style: 'normal'
  });
  fabric.nodeCanvas.registerFont(__dirname + '/test/fixtures/Ubuntu-Italic.ttf', {
    family: 'Ubuntu', weight: 'regular', style: 'italic'
  });
  fabric.nodeCanvas.registerFont(__dirname + '/test/fixtures/Ubuntu-BoldItalic.ttf', {
    family: 'Ubuntu', weight: 'bold', style: 'italic'
  });

  var canvas = new fabric.StaticCanvas(null, { width: 300, height: 250 });

  var text = new fabric.Text('regular', {
      left: 0,
      top: 50,
      fontFamily: 'Ubuntu'
  });
  canvas.add(text);

  text = new fabric.Text('bold', {
      left: 0,
      top: 100,
      fontFamily: 'Ubuntu',
      fontWeight: 'bold'
  });
  canvas.add(text);

  text = new fabric.Text('italic', {
      left: 0,
      top: 150,
      fontFamily: 'Ubuntu',
      fontStyle: 'italic'
  });
  canvas.add(text);

  text = new fabric.Text('bold italic', {
      left: 0,
      top: 200,
      fontFamily: 'Ubuntu',
      fontWeight: 'bold',
      fontStyle: 'italic'
  });
  canvas.add(text);
  canvas.renderAll();
  var out = fs.createWriteStream(__dirname + '/customfont.png');
  var stream = canvas.createPNGStream();
  stream.on('data', function(chunk) {
      out.write(chunk);
  });
```

使用节点`customfont.js`运行示例将创建一个如下图所示的图像（customfont.png）：

![4_16](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/4_16.png)

让我们仔细看看发生了什么。在fabric.nodeCanvas上，公开了JSDOM所需的节点画布库，以将node-canvas library与HTMLCanvas Api接口连接起来。对于我们要使用的每个字体文件，我们需要通过传递字体文件路径和指定字体属性的对象来向`fabric.nodeCanvas.registerFont()`注册该文件。请记住，这必须在创建Canvas本身之前发生。 

现在，我们可以通过将`fabric.Text`对象的fontFamily属性设置为字体名称来使用我们的字体。结合fontWeight和fontStyle属性，我们可以应用添加的字体。有关这些属性的更多信息，请参见[第2部分（文本）](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js.%20Part%202.md/#text)。 请注意，该示例显示了在创建新的文本对象时如何使用自定义字体，但这也适用于通过JSON加载的文本对象。 

因此，这使我们结束了有关Fabric的4部分系列的结尾。希望您现在已经掌握了足够的知识，可以创建有趣，酷，有用，有趣，具有挑战性，令人兴奋的东西！