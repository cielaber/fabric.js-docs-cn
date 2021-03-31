# Introduction to Fabric.js. Part 3

我们已经覆盖了大多数的基本知识在[第一](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js:%20Part%201.md)和[第二](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js.%20Part%202.md)这一系列的组成部分。让我们继续使用更高级的stuf！

### Groups(组合)

我们首先要谈论的是groups。Groups是Fabric最强大的功能之一。它们的确像是听起来的样子—一种将任何Fabric对象分组为单个实体的简单方法。我们为什么要这样做？当然是要将这些对象作为一个单元来使用！

还记得如何用鼠标将画布上任意数量的Fabric对象组合在一起，形成一个选择吗？分组后，所有对象都可以一起移动甚至修改。他们组成一个小组。我们可以缩放该组，旋转甚至更改其外观属性-颜色，透明度，边框等。

这正是组的用途，每当您在画布上看到这样的选择时，Fabric都会在幕后隐式创建一组对象。仅允许以编程方式提供使用组的权限。`fabric.Group`是为了什么。

让我们创建一个由2个对象组成的组，即圆圈和文本：

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
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    });

    var circle = new fabric.Circle({
        radius:100,
        fill:"#eef",
        scaleY:0.5,
        originX:"center",
        originY:"center"
    })

    var text =  new fabric.Text("hello world",{
        fontSize:30,
        originX:"center",
        originY:"center"
    })

    var group = new fabric.Group([circle,text],{
        left:100,
        top:100,
        angle:-10
    })

    this.canvas.add(group)
  },
};
</script>
<style>
</style>
```

首先，我们创建了一个“ hello world”文本对象。设置`originX`和`originY`以`'center'`让它在组内居中; 默认情况下，组成员相对于组的左上角定向。然后，以100px半径的圆圈，填充“ #eef”颜色并垂直压缩（scaleY = 0.5）。然后`fabric.Group`，我们创建了一个实例，将这两个对象以数组形式传给它，并将其位置设置为150/100和-10角度。最后，将该组添加到画布上，就像其他任何对象一样（用`canvas.add()`）。

瞧！您会在画布上看到一个看起来像椭圆的对象。注意，如何修改该对象，我们只是简单地更改了一个组的属性，为其提供了自定义的left，top和angle值。现在，您可以将此对象作为单个实体使用。

![image-20210325141832280](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325141832280.png)

现在我们在画布上有了一个组，让我们对其进行一些更改：

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    });

    var circle = new fabric.Circle({
        radius:100,
        fill:"#eef",
        scaleY:0.5,
        originX:"center",
        originY:"center"
    })

    var text =  new fabric.Text("hello world",{
        fontSize:30,
        originX:"center",
        originY:"center"
    })

    var group = new fabric.Group([circle,text],{
        left:100,
        top:100,
        angle:-10
    })

		////为了使用setFill命名setter，您需要添加可选的命名setter / getter 
		//来自src / util / named_accessors.mixins.js的代码
    group.item(0).set("fill","red");
    group.item(1).set({
        text:"trololo",
        fill:"white"
    })

    this.canvas.add(group)
  },
```

这里发生了什么？我们正在通过`item()`方法访问组中的单个对象，并修改它们的属性。第一个对象是压缩的圆圈，第二个对象是文本。让我们看看发生了什么：

![image-20210325142340342](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325142340342.png)

您现在可能已经注意到的一件事很重要，那就是组中的所有对象都相对于组的中心定位。当我们更改文本对象的文本时，即使更改了宽度，它也保持居中。如果您不希望出现这种情况，则需要指定对象的left/top坐标。在这种情况下，将根据这些坐标将它们分组在一起。

让我们创建3个圆并对其进行分组，使它们彼此水平放置：

```vue
  mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    });

    var circle1 = new fabric.Circle({
        radius:50,
        fill:"red",
        left:0
    })

    var circle2 = new fabric.Circle({
        radius:50,
        fill:"green",
        left:100
    })

    var circle3 = new fabric.Circle({
        radius:50,
        fill:"blue",
        left:200
    })

    var group2 = new fabric.Group([circle1,circle2,circle3],{
        left:50,
        top:100
    })

    this.canvas.add(group2)
  },
```

![image-20210325150931721](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325150931721.png)

使用组时要记住的另一件事是**对象**的**状态**。例如，在与图像组成组时，需要确保这些图像已完全加载。由于Fabric已经提供了用于确保加载图像的辅助方法，因此这变得相当容易：

```vue
<template>
  <div class="groups">
    <canvas id="canvas"></canvas>
  </div>
</template>
<script>
import { fabric } from "fabric";
import {imgUrl} from "../assets/1"
export default {
  data() {
    return {
      canvas: "",
    };
  },
  mounted() {
    var canvas = (this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    }));
 
    fabric.Image.fromURL(imgUrl, function (img) {
      var img1 = img.scale(0.1).set({ left: 100, top: 100 });

      fabric.Image.fromURL(imgUrl, function (img) {
        var img2 = img.scale(0.1).set({ left: 175, top: 175 });

        fabric.Image.fromURL(imgUrl, function (img) {
          var img3 = img.scale(0.1).set({ left: 250, top: 250 });

          canvas.add(
            new fabric.Group([img1, img2, img3], { left: 100, top: 50 })
          );
        });
      });
    });
  },
};
</script>
<style>
</style>
```

（说明：这里图片可能会出错，我放本地图片地址会报“Cannot read property 'naturalWidth' of null”的错误，直接放网络图片地址会报“Failed to execute 'texImage2D' on 'WebGLRenderingContext': The image element contains cross-origin data, and may not be loaded.”的错误。

我采用了[这里](https://www.jq22.com/web-skill78)的解决方法，将本地图片转为Base64格式，且为防止代码过长，我将base64编码存在了asset/1.js中，所以以上代码我直接通过import导入图片的base64地址。）

![image-20210325155407490](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325155407490.png)

那么在与groups一起工作时还有哪些其他方法可用？有`getObjects()`方法，其工作原理与之完全相同，`fabric.Canvas#getObjects()`并返回一组中所有对象的数组。有`size()`代表组中的所有对象的数量。还有`contains()`，它允许检查特定对象是否在group中。还有`item()`，我们前面看到的，允许在一个group中检索特定对象。还有`forEachObject()`，再次反映`fabric.Canvas#forEachObject`，只是相对于组对象。最后，有一种`add()`和`remove()`方法可以相应地从组中添加和删除对象。

您可以通过两种方式从group中添加/删除对象-更新或不更新group dimensions/position。我们建议使用更新dimensions，除非您正在执行批处理操作，并且在处理过程中组的宽度/高度不存在问题时没有问题。

要将矩形添加到组的中心，请执行以下操作：

```vue
mounted() {
    var canvas = this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    });

    var circle = new fabric.Circle({
      radius: 100,
      fill: "#eef",
      scaleY: 0.5,
      originX: "center",
      originY: "center",
    });

    var text = new fabric.Text("hello world", {
      fontSize: 30,
      originX: "center",
      originY: "center",
    });

    var group = new fabric.Group([circle, text], {
      left: 100,
      top: 100,
      angle: -10,
    });

    group.add(new fabric.Rect({
        width:100,
        height:100,
        fill:"orange",
        originX:"center",
        originY:"center",
    }))

    canvas.add(group)
  },
```

![image-20210325163059452](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325163059452.png)

要在组中心之外添加100px的矩形，请执行以下操作：

```vue
mounted() {
    var canvas = this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    });

    var circle = new fabric.Circle({
      radius: 100,
      fill: "#eef",
      scaleY: 0.5,
      originX: "center",
      originY: "center",
    });

    var text = new fabric.Text("hello world", {
      fontSize: 30,
      originX: "center",
      originY: "center",
    });

    var group = new fabric.Group([circle, text], {
      left: 100,
      top: 100,
      angle: -10,
    });

    group.add(new fabric.Rect({
        width:100,
        height:100,
        fill:"orange",
        originX:"center",
        originY:"center",
        left:100,
        top:100,
    }))

    canvas.add(group)
  },
```

![image-20210325162738731](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325162738731.png)

要将矩形添加到组的中心并更新组的尺寸，请执行以下操作：

```vue
mounted() {
    var canvas = this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    });

    var circle = new fabric.Circle({
      radius: 100,
      fill: "#eef",
      scaleY: 0.5,
      originX: "center",
      originY: "center",
    });

    var text = new fabric.Text("hello world", {
      fontSize: 30,
      originX: "center",
      originY: "center",
    });

    var group = new fabric.Group([circle, text], {
      left: 100,
      top: 100,
      angle: -10,
    });

    group.addWithUpdate(new fabric.Rect({
        width:100,
        height:100,
        fill:"orange",
        originX:"center",
        originY:"center",
        left:group.get("left"),
        top:group.get("top"),
    }))

    canvas.add(group)
  },
```

![image-20210325163018435](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325163018435.png)

要在距组中心100px处添加矩形并更新组的尺寸，请执行以下操作：

mounted() {
    var canvas = this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 300,
      backgroundColor: "#ddd",
    });

```vue
var circle = new fabric.Circle({
  radius: 100,
  fill: "#eef",
  scaleY: 0.5,
  originX: "center",
  originY: "center",
});

var text = new fabric.Text("hello world", {
  fontSize: 30,
  originX: "center",
  originY: "center",
});

var group = new fabric.Group([circle, text], {
  left: 100,
  top: 100,
  angle: -10,
});

group.addWithUpdate(new fabric.Rect({
  width:100,
  height:100,
  fill:"orange",
  originX:"center",
  originY:"center",
  left:group.get("left")+100,
  top:group.get("top")+100,
    }))

canvas.add(group)
  },
```
![image-20210325163245096](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325163245096.png)

最后，如果您想使用画布上已经存在的对象创建一个组，则需要首先克隆它们：

```vue
var circle = new fabric.Circle({
  radius: 100,
  fill: "#eef",
  scaleY: 0.5,
  originX: "center",
  originY: "center",
});

var text = new fabric.Text("hello world", {
  fontSize: 30,
  originX: "center",
  originY: "center",
});

canvas.add(circle, text);

// create a group with copies of existing (2) objects
var group = new fabric.Group([
//   canvas.item(0).clone(),
//   canvas.item(1).clone(),
  fabric.util.object.clone(canvas.item(0)),
  fabric.util.object.clone(canvas.item(1)),
]);

// remove all objects and re-render
canvas.clear().renderAll();

// add group onto canvas
canvas.add(group);
  },
```

（说明：这里没有使用原文中的`canvas.item(0).clone()`方法，因为无法生效，而是使用`fabric.util.object.clone()`方法，参考自：http://www.voidcn.com/article/p-hzvdfsee-bwo.html）

![image-20210325170119640](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210325170119640.png)

### Serialization(序列化)

一旦开始构建某种有状态的应用程序（可能允许用户将画布内容的结果保存在服务器上，或将内容流式传输到其他客户端），您将需要**canvas序列化**。如果仍然要发送画布内容？也是可以的，有一个选项可以将画布导出到图像。但是上传图片到服务器无疑是相当占用带宽的，论大小，没有什么可以比得过文本了，这就是为什么Fabric为canvas画布序列化/反序列化提供了极好的支持。

#### toObject, toJSON

Fabric中的canvas序列化方法主要是`toObject（）`和`toJSON（）`方法。我们来看一个简单的例子，首先序列化一个空的画布：

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
    this.canvas = new fabric.Canvas("canvas");

    console.log(JSON.stringify(this.canvas));
    //{"version":"4.3.1","objects":[]}
  },
};
</script>
<style>
</style>
```

我们正在使用ES5 `JSON.stringify()`方法，该方法在传递的对象上隐式调用`toJSON`方法（如果存在）。由于Fabric中的canvas实例具有`toJSON`方法，就像我们调用`JSON.stringify(canvas.toJSON())`一样。 

请注意，返回的字符串代表空canvas。它采用JSON格式，并且基本上由“objects”和“background”属性组成。 “objects”当前为空，因为画布上没有任何内容，并且背景具有默认的透明值（“ rgba（0，0，0，0）”）。 

让我们为画布提供不同的背景，看看情况如何变化：

```vue
  mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

    console.log(JSON.stringify(this.canvas));
    //{"version":"4.3.1","objects":[],"background":"#ddd"}
  },
```

正如人们所料，画布表示现在反映了新的背景色。现在，让我们添加一些对象！

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

    this.canvas.add(new fabric.Rect({
        left:50,
        top:50,
        height:20,
        width:20,
        fill:"green"
    }))

    console.log(JSON.stringify(this.canvas));
  },
```

..日志的输出为：

```
{"version":"4.3.1","objects":[{"type":"rect","version":"4.3.1","originX":"left","originY":"top","left":50,"top":50,"width":20,"height":20,"fill":"green","stroke":null,"strokeWidth":1,"strokeDashArray":null,"strokeLineCap":"butt","strokeDashOffset":0,"strokeLineJoin":"miter","strokeUniform":false,"strokeMiterLimit":4,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"shadow":null,"visible":true,"backgroundColor":"","fillRule":"nonzero","paintFirst":"fill","globalCompositeOperation":"source-over","skewX":0,"skewY":0,"rx":0,"ry":0}],"background":"#ddd"}
```

哇哦。乍一看变化很大，但仔细看，我们发现新添加的对象，现在是“objects”数组的一部分，序列化为JSON。请注意，它的表示方式包含了它的所有视觉特征-左、上、宽、高、填充、笔划等等。
如果我们要添加另一个对象（例如，矩形旁边的一个红色圆），您将看到表示方式相应地发生了变化：

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

    this.canvas.add(new fabric.Rect({
        left:50,
        top:50,
        height:20,
        width:20,
        fill:"green"
    }))

    this.canvas.add(new fabric.Circle({
        left:100,
        top:100,
        radius:50,
        fill:"red"
    }))

    console.log(JSON.stringify(this.canvas));
  },
```

..日志的输出为：

> {"version":"4.3.1","objects":[{**"type":"rect"**,"version":"4.3.1","originX":"left","originY":"top","left":50,"top":50,"width":20,"height":20,"fill":"green","stroke":null,"strokeWidth":1,"strokeDashArray":null,"strokeLineCap":"butt","strokeDashOffset":0,"strokeLineJoin":"miter","strokeUniform":false,"strokeMiterLimit":4,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"shadow":null,"visible":true,"backgroundColor":"","fillRule":"nonzero","paintFirst":"fill","globalCompositeOperation":"source-over","skewX":0,"skewY":0,"rx":0,"ry":0},{**"type":"circle"**,"version":"4.3.1","originX":"left","originY":"top","left":100,"top":100,"width":100,"height":100,"fill":"red","stroke":null,"strokeWidth":1,"strokeDashArray":null,"strokeLineCap":"butt","strokeDashOffset":0,"strokeLineJoin":"miter","strokeUniform":false,"strokeMiterLimit":4,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"shadow":null,"visible":true,"backgroundColor":"","fillRule":"nonzero","paintFirst":"fill","globalCompositeOperation":"source-over","skewX":0,"skewY":0,"radius":50,"startAngle":0,"endAngle":6.283185307179586}],"background":"#ddd"}

我突出显示了`“ type”：“ rect”`和`“ type”：“ circle”`部分，以便您可以更好地看到这些对象的位置。尽管一开始看起来可能有很多输出，但是与图像序列化相比，这没什么。为了进行比较，让我们看一下用`canvas.toDataURL('png')`获得的字符串的大约1/10(!)。

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

    this.canvas.add(new fabric.Rect({
        left:50,
        top:50,
        height:20,
        width:20,
        fill:"green"
    }))

    this.canvas.add(new fabric.Circle({
        left:100,
        top:100,
        radius:50,
        fill:"red"
    }))

    console.log(this.canvas.toDataURL("png"));
  },
```

```
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAZAAAAGQCAYAAACAvzbMAAAZvUlEQVR4Xu3dS6xW5dUH8AflWkChIhRbUhqiDkz8IA6sCQNTL4lSRyqmXsLApCWkXogx1YkhTrQxBsWG0CYOiGIj6siiiZfGAYl1YOAzcaCGFEMrFbGgQJGL8GXzjRoBz9lnve95...
```

…还有许多字符未显示。

您可能想知道为什么还会有`fabric.Canvas＃toObject`。很简单，`toObject`仅以实际对象的形式返回与`toJSON`相同的表示形式，而没有字符串序列化。例如，以较早的仅带有绿色矩形的画布为例，`canvas.toObject()`的输出是这样的：

```json
{ "background" : "rgba(0, 0, 0, 0)",
  "objects" : [
    {
      "angle" : 0,
      "fill" : "green",
      "flipX" : false,
      "flipY" : false,
      "hasBorders" : true,
      "hasControls" : true,
      "hasRotatingPoint" : false,
      "height" : 20,
      "left" : 50,
      "opacity" : 1,
      "overlayFill" : null,
      "perPixelTargetFind" : false,
      "scaleX" : 1,
      "scaleY" : 1,
      "selectable" : true,
      "stroke" : null,
      "strokeDashArray" : null,
      "strokeWidth" : 1,
      "top" : 50,
      "transparentCorners" : true,
      "type" : "rect",
      "width" : 20
    }
  ]
}
```

如您所见，`toJSON`输出本质上是一个字符串化的`toObject`输出。现在，有趣的是（有用的！）事情是`toObject`的输出既聪明又懒。您在“objects”数组中看到的是迭代所有画布对象并将它们委托给它们自己的`toObject`方法的结果。` fabric.Path`有自己的`toObject` —返回路径的“points”数组，`fabric.Image`有自己的`toObject` —返回图像的“ src”属性。以一种真正的面向对象的方式，所有对象都可以序列化自己。 

这意味着，当您创建自己的“class”或仅需要自定义对象的序列化表示形式时，您要做的就是使用`toObject`方法-完全替换它或对其进行扩展。让我们尝试一下：

```vue
  mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

    var rect1 = new fabric.Rect();
    rect1.toObject = function(){
        return {name:"trololo"}
    };

    this.canvas.add(rect1);
    console.log(JSON.stringify(this.canvas));
  },
```

..日志输出为：

```
{"version":"4.3.1","objects":[{"name":"trololo"}],"background":"#ddd"}
```

如您所见，objects数组现在有了矩形的自定义表示。这种覆盖可能不是很有用（尽管提到了重点），我们还是用额外的属性来扩展矩形的toObject方法吧。

```vue
  mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

    var rect2 = new fabric.Rect()
    rect2.toObject = (function(toObject){
        return function(){
            return fabric.util.object.extend(toObject.call(this),{
                name:this.name
            })
        }
    })(rect2.toObject);

    this.canvas.add(rect2)

    rect2.name = "trololo";

    console.log(JSON.stringify(this.canvas));
  },
```

..日志输出为：

```
{"version":"4.3.1","objects":[{"type":"rect","version":"4.3.1","originX":"left","originY":"top","left":0,"top":0,"width":0,"height":0,"fill":"rgb(0,0,0)","stroke":null,"strokeWidth":1,"strokeDashArray":null,"strokeLineCap":"butt","strokeDashOffset":0,"strokeLineJoin":"miter","strokeUniform":false,"strokeMiterLimit":4,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"shadow":null,"visible":true,"backgroundColor":"","fillRule":"nonzero","paintFirst":"fill","globalCompositeOperation":"source-over","skewX":0,"skewY":0,"rx":0,"ry":0,"name":"trololo"}],"background":"#ddd"}
```

我们使用附加属性“name”扩展了对象现有的`toObject`方法，因此该属性现在是`toObject`输出的一部分，以canvas JSON表示形式出现。值得一提的是，如果您像这样扩展对象，则还需要确保对象的“class”（在这种情况下为`fabric.Rect`）在“ stateProperties”数组中具有此属性，以便从字符串形式中加载画布并将其解析并正确添加到对象。 

您可以将对象标记为不可导出，将`excludeFromExport`设置为`true`。这样，您在画布上可以拥有的一些辅助对象将不会在序列化过程中保存。

#### toSVG

另一种有效的基于文本的画布表示形式为SVG格式。由于Fabric专注于在画布上进行SVG解析和渲染，因此只有将其设为双向过程并提供画布到SVG的转换才有意义。让我们将相同的矩形添加到画布上，看看toSVG方法返回了哪种表示形式：

```vue
  mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

    this.canvas.add(new fabric.Rect({
        left:50,
        top:50,
        height:20,
        width:20,
        fill:"green"
    }))
    console.log(this.canvas.toSVG());
  },
```

..日志输出为：

```
<?xml version="1.0" encoding="UTF-8" standalone="no" ?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="400" height="400" viewBox="0 0 400 400" xml:space="preserve">
<desc>Created with Fabric.js 4.3.1</desc>
<defs>
</defs>
<rect x="0" y="0" width="100%" height="100%" fill="#ddd"></rect>
<g transform="matrix(1 0 0 1 60.5 60.5)"  >
<rect style="stroke: none; stroke-width: 1; stroke-dasharray: none; stroke-linecap: butt; stroke-dashoffset: 0; stroke-linejoin: miter; stroke-miterlimit: 4; fill: rgb(0,128,0); fill-rule: nonzero; opacity: 1;"  x="-10" y="-10" rx="0" ry="0" width="20" height="20" />
</g>
</svg>
```

就像`toJSON`和`toObject`一样，`toSVG`（在画布上调用时）将其逻辑委托给每个单独的对象，并且每个单独的对象都有其自己的`toSVG`方法，该方法专用于对象类型。如果您需要修改或扩展对象的SVG表示形式，则可以使用`toSVG`来完成与`toObject`相同的操作。 

与Fabric专有的`toObject `/ `toJSON`相比，SVG表示的优点是您可以将其放入任何支持SVG的渲染器（浏览器，应用程序，打印机，照相机等）中，并且应该可以正常工作。但是，使用`toObject` /` toJSON`，您首先需要将其加载到画布上。说到将内容加载到画布上，现在我们可以将画布序列化为高效的文本块了，我们将如何重新加载到画布上呢？

### Deserialization, SVG parser(反序列化，SVG解析器)

与序列化类似，有两种从字符串加载画布的方法：从JSON表示或从SVG加载。使用JSON表示形式时，有`fabric.Canvas＃loadFromJSON`和`fabric.Canvas＃loadFromDatalessJSON`方法。使用SVG时，有`fabric.loadSVGFromURL`和`fabric.loadSVGFromString`。 

请注意，前两个方法是实例方法，可以直接在canvas实例上调用，而后两个方法是静态方法，可以在“ fabric”对象上而不是在画布上调用。 这些方法没什么可说的。它们的工作与您期望的完全一样。让我们以画布上的先前JSON输出为例，并将其加载到干净的画布上：

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });

		this.canvas.loadFromJSON('{"version":"4.3.1","objects":[{"type":"rect","version":"4.3.1","originX":"left","originY":"top","left":50,"top":50,"width":20,"height":20,"fill":"green","stroke":null,"strokeWidth":1,"strokeDashArray":null,"strokeLineCap":"butt","strokeDashOffset":0,"strokeLineJoin":"miter","strokeUniform":false,"strokeMiterLimit":4,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"shadow":null,"visible":true,"backgroundColor":"","fillRule":"nonzero","paintFirst":"fill","globalCompositeOperation":"source-over","skewX":0,"skewY":0,"rx":0,"ry":0},{"type":"circle","version":"4.3.1","originX":"left","originY":"top","left":100,"top":100,"width":100,"height":100,"fill":"red","stroke":null,"strokeWidth":1,"strokeDashArray":null,"strokeLineCap":"butt","strokeDashOffset":0,"strokeLineJoin":"miter","strokeUniform":false,"strokeMiterLimit":4,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"shadow":null,"visible":true,"backgroundColor":"","fillRule":"nonzero","paintFirst":"fill","globalCompositeOperation":"source-over","skewX":0,"skewY":0,"radius":50,"startAngle":0,"endAngle":6.283185307179586}],"background":"#ddd"}')
  },
```

..两个物体“神奇地”出现在画布上：

![image-20210329115709193](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329115709193.png)

因此，从字符串加载画布非常容易。但是那种看起来很奇怪的`loadFromDatalessJSON`方法呢？与我们刚刚使用的`loadFromJSON`有什么不同？为了了解为什么需要此方法，我们需要查看具有或多或少复杂路径对象的序列化画布。像这个：

![image-20210329120453141](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329120453141.png)

..，此形状的JSON.stringify（canvas）输出为：

```
{"objects":[{"type":"path","left":184,"top":177,"width":175,"height":151,"fill":"#231F20","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":-19,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"path":[["M",39.502,61.823],["c",-1.235,-0.902,-3.038,-3.605,-3.038,-3.605],["s",0.702,0.4,3.907,1.203],["c",3.205,0.8,7.444,-0.668,10.114,-1.97],["c",2.671,-1.302,7.11,-1.436,9.448,-1.336],["c",2.336,0.101,4.707,0.602,4.373,2.036],["c",-0.334,1.437,-5.742,3.94,-5.742,3.94],["s",0.4,0.334,1.236,0.334],["c",0.833,0,6.075,-1.403,6.542,-4.173],["s",-1.802,-8.377,-3.272,-9.013],["c",-1.468,-0.633,-4.172,0,-4.172,0],["c",4.039,1.438,4.941,6.176,4.941,6.176],["c",-2.604,-1.504,-9.279,-1.234,-12.619,0.501],["c",-3.337,1.736,-8.379,2.67,-10.083,2.503],["c",-1.701,-0.167,-3.571,-1.036,-3.571,-1.036],["c",1.837,0.034,3.239,-2.669,3.239,-2.669],["s",-2.068,2.269,-5.542,0.434],...
```

..那只是整个输出的小部分！

这里发生了什么？好吧，事实证明，这个`fabric.Path`实例-这种形状-实际上由数百条贝塞尔曲线组成，这些贝塞尔曲线决定了渲染的精确程度。 JSON表示形式中的所有这些[“ c”，0,2.67，-0.979,5.253，-2.048,9.079]块均对应于此类曲线中的每条曲线。而且当它们有数百个（甚至数千个）时，画布表示形式最终会变得非常庞大。 

该怎么办？ 

这是`fabric.Canvas＃toDatalessJSON`派上用场的时候。让我们尝试一下：

```js
canvas.item(0).sourcePath = '/assets/dragon.svg';
console.log(JSON.stringify(canvas.toDatalessJSON()));
```

..日志的输出为：

> {"objects":[{"type":"path","left":143,"top":143,"width":175,"height":151,"fill":"#231F20","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":-19,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,**"path":"/assets/dragon.svg"**}],"background":"rgba(0, 0, 0, 0)"}

好吧，那肯定很小！所以发生了什么事？请注意，在调用`toDatalessJSON`之前，我们为path（龙形）对象提供值为“ /assets/dragon.svg”的“ sourcePath”属性。然后，当我们调用`toDatalessJSON`时，先前输出中的整个巨大路径字符串（数百条路径命令）被替换为单个“ dragon.svg”字符串。您可以看到上面突出显示的内容。 

在处理许多复杂形状时，`toDatalessJSON`允许我们进一步减少画布表示，并使用指向SVG的简单链接替换巨大的路径数据表示。 

现在回到`loadFromDatalessJSON`方法...您可能会猜到，它只是允许从无数据版本的画布表示中加载画布。 `loadFromDatalessJSON`非常了解如何获取那些“path”字符串（例如“ /assets/dragon.svg”），加载它们并将它们用作相应路径对象的数据。

现在，让我们看一下SVG加载方法。我们可以使用字符串或URL：

```js
fabric.loadSVGFromString('...', function(objects, options) {
  var obj = fabric.util.groupSVGElements(objects, options);
  canvas.add(obj).renderAll();
});
```

第一个参数是SVG字符串，第二个参数是回调函数。当解析和加载SVG并接收2个参数（objects和options）时，将调用该回调。 objects包含从SVG解析的对象数组-paths,path groups（用于复杂对象）,images,text等。为了将所有这些对象分组为一个内聚的集合，并使其与SVG文档中的外观相同，我们使用`fabric.util.groupSVGElements`传递对象和选项。作为回报，我们获得`fabric.Path`或`fabric.Group`的实例，然后可以将其添加到画布上。 

`fabric.loadSVGFromURL`的工作方式相同，只不过您传递的是包含URL而不是SVG内容的字符串。请注意，Fabric将尝试通过`XMLHttpRequest`获取该URL，因此SVG需要符合通常的SOP规则。

### Subclassing

由于Fabric是按照真正的面向对象的方式构建的，因此可以使子类化和扩展变得简单自然。从本系列的第1部分中可以知道，Fabric中存在对象的现有层次结构。所有2D对象（paths,images,text等）都从fabric.Object继承，并且某些“类”（例如`fabric.IText`）甚至形成3级继承。 那么，我们将如何在Fabric中对现有的“类”之一进行子类化呢？也许甚至创建我们自己的？ 对于此任务，我们需要`fabric.util.createClass`实用程序方法。` createClass`只是对Javascript原型继承的简单抽象。让我们首先创建一个简单的Point“ class”：

```js
var Point = fabric.util.createClass({
  initialize:function(x,y){
    this.x = x || 0;
    this.y = y || 0;
  },
  toString:function(){
    return this.x +"/" +this.y;
  }
})
```

`createClass`接受一个对象，并使用该对象的属性创建具有实例级属性的“类”。唯一经过特殊处理的属性是“初始化”，它用作构造函数。因此，现在初始化Point时，我们将创建一个具有“ x”和“ y”属性以及“ toString”方法的实例：

```js
var point = new Point(10,20);
console.log(point.x);//10
console.log(point.y);//20
console.log(point.toString());//10/20
```

如果我们要创建“ Point”类的子级（比如说一个彩色的点），则可以使用createClass，如下所示：

```js
var ColoredPoint = fabric.util.createClass(Point,{
  initialize:function(x,y,color){
    this.callSuper("initialize",x,y);
    this.color = color || "#000"
  },
  toString:function(){
    return this.callSuper("toString") + "(color:" +this.color+")";
  }
})
```

请注意，现在如何将具有实例级属性的对象作为第二个参数传递。第一个参数接收Point“类”，该点告诉`createClass`将该点用作该类的父“类”。为了避免重复，我们使用`callSuper`方法，该方法调用父“类”的方法。这意味着，如果我们要更改`Point`，则更改也将传播到`ColoredPoint`。要查看`ColoredPoint`的实际效果：

```js
var redPoint = new ColoredPoint(15,33,"#f55");
console.log(redPoint.x);//15
console.log(redPoint.y);//33
console.log(redPoint.color);//#f55
console.log(redPoint.toString());//15/33(color:#f55)
```

现在我们开始创建自己的“类”和“子类”，让我们看看如何与已经存在的Fabric一起使用。例如，让我们创建一个`LabeledRect`“类”，该类实际上是一个具有某种与之关联的标签的矩形。当在画布上渲染时，该标签将被表示为矩形内的文本。与上一个带有圆圈和文字的小组示例相似。在使用Fabric时，您会注意到可以通过使用组或使用自定义类来实现这样的组合抽象。

```js
var LabeledRect = fabric.util.createClass(fabric.Rect,{
  type:"labeledRect",
  initialize:function(options){
    options || (options = {});
    this.callSuper('initialize',options);
    this.set("label",options.label || '');
  },

  toObject:function(){
    return fabric.util.obejct.extend(this.callSuper("toObject"),{
      label:this.get("label")
    });
  },

  _render:function(ctx){
    this.callSuper("_render",ctx);
    ctx.font = "20px Helvetica";
    ctx.fillStyle = "#333";
    ctx.fillText(this.label,-this.width/2,-this.height/2+20);
  }
})
```

似乎这里发生了很多事情，但是实际上很简单。 

首先，我们将父“类”指定为`fabric.Rect`，以利用其渲染功能。接下来，我们定义“ type”属性，将其设置为“ labeledRect”。这只是为了保持一致性，因为所有Fabric对象都具有type属性（矩形，圆形，路径，文本等）。然后，已经熟悉了构造函数（`initialize`），在该构造函数中，我们再次使用`callSuper`。此外，我们将对象的标签设置为通过选项传递的任何值。最后，剩下2种方法-`toObject`和`_render`。如您从序列化一章已经知道的，`toObject`负责实例的对象（和JSON）表示。由于`LabeledRect`具有与常规rect相同的属性，但也具有标签，因此我们扩展了父对象的`toObject`方法，并简单地向其中添加了标签。最后但并非最不重要的一点是，`_render`方法是负责实际绘制实例的原因。其中还有另一个`callSuper`调用，它是呈现矩形的内容，另外还有3行文本呈现逻辑。

现在，如果我们要渲染这样的对象：

```js
this.canvas = new fabric.Canvas("canvas", {
  width: 300,
  height: 300,
  backgroundColor: "#ddd",
});

var labeledRect = new LabeledRect({
  width:100,
  height:50,
  left:100,
  top:100,
  label:'test',
  fill:"#faa"
});
this.canvas.add(labeledRect)
```

..我们会得到这个：

![image-20210329151752796](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329151752796.png)

更改标签值或任何其他常规矩形属性显然可以按预期工作：

```js
labeledRect.set({
  label:"trololo",
  fill:"#aaf",
  rx:10,
  ry:10
})
```

![image-20210329152008348](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210329152008348.png)

当然，在这一点上，您可以随意更改此“类”的行为。例如，将某些值设为默认值，以避免每次将它们传递给构造函数。或使某些可配置属性在实例上可用。如果确实使其他属性可配置，则可能要在`toObject`中说明它们并进行`initialize`：

```js
...
initialize: function(options) {
  options || (options = { });

  this.callSuper('initialize', options);

  // give all labeled rectangles fixed width/heigh of 100/50
  this.set({ width: 100, height: 50 });

  this.set('label', options.label || '');
}
...
_render: function(ctx) {

  // make font and fill values of labels configurable
  ctx.font = this.labelFont;
  ctx.fillStyle = this.labelFill;

  ctx.fillText(this.label, -this.width/2, -this.height/2 + 20);
}
...
```

为了克隆和保存/还原此类，您需要添加一个fromObject静态方法，并在其之上添加要添加到主fabricObject的子类：

```js
// standard options type:
fabric.labeledRect.fromObject = function(object, callback) {
  return fabric.Object._fromObject('LabeledRect', object, callback);
}
  ...
  // argument + options type:
  // in this example aProp is the property in the object that contains the value
  // that goes in someValue in `new fabric.MyClass(someValue, options)`
fabric.labeledRect.fromObject = function(object, callback) {
  return fabric.Object._fromObject('LabeledRect', object, callback, 'aProp');
}
```

关于这一点，我总结了本系列的第3部分，其中我们探讨了Fabric的一些更高级的方面。在组，类和（反）序列化的帮助下，您可以将您的应用提升到一个全新的水平。