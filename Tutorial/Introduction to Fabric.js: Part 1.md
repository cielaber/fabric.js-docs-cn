# Introduction to Fabric.js: Part 1



### Introduction to Fabric.js. Part 1.(介绍Fabric.js第一部分)

今天，我想向您介绍Fabric.js —一个功能强大的Javascript库，使使用HTML5 canvas的工作变得轻而易举。 Fabric提供了画布缺少的对象模型，以及SVG解析器，交互性层和一整套其他必不可少的工具。这是一个完全开放源代码的项目，已获得麻省理工学院（MIT）的许可，多年来有许多贡献。

在发现使用原生canvas API的烦恼之后，Fabric于2010年左右开始。最初的作者正在为printio.ru创建一个交互式设计编辑器-他的创业公司允许用户设计自己的服装。他们想要的那种交互性仅在那时存在于Flash应用程序中。即使是现在，也很少有人能接近Fabric所能提供的功能。 

让我们仔细看看！

### Why fabric?(什么是Fabric？)

如今，Canvas允许我们在网络上创建一些绝对惊人的图形。但是它提供的API是极令人失望地低级。如果我们只是想在画布上绘制一些基本形状而忘记它们，那是一回事。但是，只要需要进行任何形式的交互，随时更改图片或绘制更复杂的形状，情况就会发生巨大变化。

Fabric旨在解决这个问题。 

原生canvas方法仅允许我们触发简单的图形命令，盲目地修改整个画布位图。想画一个矩形吗？使用fillRect（left，top，width，height）。要画一条线吗？使用moveTo（left，top）和lineTo（x，y）的命令组合。就像我们正在用刷子画画布一样，在很少的控制下将越来越多的油层涂在上面。 

Fabric没有在如此低的级别上进行操作，而是在原生canvas方法之上提供了简单但功能强大的对象模型。它负责画布的状态和渲染，并让我们直接使用“对象”。 

让我们看一个简单的例子来说明这种差异。假设我们要在画布上的某个地方绘制一个红色矩形。这是我们如何使用原生canvas API进行的操作。

```vue
<template>
  <div class="app">
    <canvas id="canvas" width="300" height="200"></canvas>
  </div>
</template>

<script>
export default {
  mounted() {
    var canvasEl = document.getElementById("canvas");
    canvasEl.style.background = 'grey'
    var ctx = canvasEl.getContext("2d");
    ctx.fillStyle = 'red';
    ctx.fillRect(100,100,20,20);
  },
};
</script>
<style>
</style>
```

现在，让我们看一下使用Fabric进行相同的操作：

```vue
<template>
  <div class="app">
    <canvas id="canvas" width="300" height="200"></canvas>
  </div>
</template>

<script>
import {fabric} from "fabric"
export default {
  mounted(){
    var canvas = new fabric.Canvas("canvas",{backgroundColor:"grey"});
    var rect = new fabric.Rect({
      left:100,
      top:100,
      fill:"red",
      width:20,
      height:20,
    });
    canvas.add(rect);
  }
}
</script>

<style>

</style>
```

![屏幕快照 2021-03-19 上午11.36.35](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202021-03-19%20%E4%B8%8A%E5%8D%8811.36.35.png)

此时，大小几乎没有区别-这两个示例非常相似。但是，您已经可以看到使用画布的方法有多么不同。使用原生方法，我们可以在上下文上操作——一个表示整个画布位图的对象。在Fabric中，我们对对象进行操作——实例化它们，更改其属性并将其添加到画布。您会看到这些对象是Fabric土地上的一等公民。

但是渲染纯红色矩形实在太无聊了。我们至少可以从中获得一些乐趣！也许，稍微旋转一下？ 

让我们尝试45度。首先，使用原生canvas方法：

```vue
mounted() {
    var canvasEl = document.getElementById("canvas");
    canvasEl.style.background = 'grey'
    var ctx = canvasEl.getContext("2d");
    ctx.fillStyle = 'red';
    // ctx.fillRect(100,100,20,20);

    //偏移角度
    ctx.translate(100,100);
    ctx.rotate(Math.PI/180*45);
    ctx.fillRect(-10,-10,20,20);
  },
```

![image-20210319135900911](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319135900911.png)

现在使用Fabric：

```vue
mounted(){
    var canvas = new fabric.Canvas("canvas",{backgroundColor:"grey"});
    var rect = new fabric.Rect({
      left:100,
      top:100,
      fill:"red",
      width:20,
      height:20,
      angle:45 //偏移角度
    });
    canvas.add(rect);
  }

```

![image-20210319135813749](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319135813749.png)

（注意这里两个是不一样的，下面作者说使用canvas时为了让为了矩形保持在原位需要偏移（-10,-10）但其实不需要这样做。最后直接ctx.fillRect(0,0,20,20)才是一样的效果。）

这里发生了什么？

我们在Fabric中要做的就是将对象的“角度”值更改为45。但是，使用原生canvas方法，事情会变得更加“有趣”。请记住，我们不能对对象进行操作。相反，我们调整整个画布位图的位置和角度（ctx.translate，ctx.rotate）以适合我们的需求。然后，我们再次绘制矩形，但要记住适当地偏移位图（-10，-10），以便它仍在（100,100）点位处渲染。作为一项额外的练习，在旋转画布位图时，我们必须将度数转换为弧度。

我相信您已经开始确切地了解为什么存在Fabric以及它隐藏了多少个低级样板。

但是，让我们看看另一个示例——跟踪画布状态。

如果在某个时候，我们想将现在熟悉的红色矩形移动到画布上稍有不同的位置，该怎么办？在无法对对象进行操作的情况下，我们将如何做到这一点？我们仅在画布位图上调用另一个fillRect吗？

不完全是。调用另一个fillRect命令实际上会在画布上已绘制的内容的上方绘制矩形。还记得我之前提到过用画笔绘画吗？为了“移动”它，我们需要首先擦除先前绘制的内容，然后在新位置绘制矩形。

```vue
mounted() {
    var canvasEl = document.getElementById("canvas");
    canvasEl.style.background = 'grey'
    var ctx = canvasEl.getContext("2d");
    ctx.fillStyle = 'red';
    // ctx.fillRect(100,100,20,20);

    //位移
    ctx.strokeRect(100,100,20,20);
    ctx.clearRect(0,0,canvasEl.width,canvasEl.height);
    ctx.fillRect(20,50,20,20);
  },
```

那我们将如何使用Fabric做到这一点？

```vue
mounted(){
    var canvas = new fabric.Canvas("canvas",{backgroundColor:"grey"});
    var rect = new fabric.Rect({
      left:100,
      top:100,
      fill:"red",
      width:20,
      height:20,
    });
    canvas.add(rect);
		// 位移
    rect.set({left:20,top:50});
    canvas.renderAll()
  }
```

![image-20210319143139135](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319143139135.png)

注意一个非常重要的区别。使用Fabric，在尝试修改任何内容前，我们不再需要删除内容。我们仍然使用对象，只需更改其属性，然后重新渲染画布即可获得“新图片”。

### Objects(对象)

我们已经看到了如何通过实例化fabric.Rect构造函数来使用矩形。但是，Fabric当然也涵盖了所有其他基本形状——圆形，三角形，椭圆形等。它们全部都在fabric“命名空间”下暴露为fabric.Circle，circle.Triangle，fabric.Ellipse等。

Fabric提供的7中基本形状：

- [fabric.Circle](http://fabricjs.com/docs/fabric.Circle.html)
- [fabric.Ellipse](http://fabricjs.com/docs/fabric.Ellipse.html)
- [fabric.Line](http://fabricjs.com/docs/fabric.Line.html)
- [fabric.Polygon](http://fabricjs.com/docs/fabric.Polygon.html)
- [fabric.Polyline](http://fabricjs.com/docs/fabric.Polyline.html)
- [fabric.Rect](http://fabricjs.com/docs/fabric.Rect.html)
- [fabric.Triangle](http://fabricjs.com/docs/fabric.Triangle.html)

想画一个圆吗？只需创建一个圆形对象，然后将其添加到画布即可。与其他任何基本形状相同：

```vue
 mounted(){
    var canvas = new fabric.Canvas("canvas",{backgroundColor:"grey"});
    // 画圆
    var circle = new fabric.Circle({
      radius:20,
      fill:"green",
      left:100,
      top:100,
    })
    // 画三角形
    var triangle = new fabric.Triangle({
      width:20,
      height:30,
      fill:'blue',
      left:50,
      top:50
    })

    canvas.add(circle,triangle)
  }
```

 ![image-20210319151214103](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319151214103.png)

我们有一个在(100,100)位置绘制的绿色圆圈和在(50,50)位置绘制的蓝色三角形。

#### Manipulating objects(操作对象)

创建图形对象（矩形，圆形或其他形状）当然只是开始。在某个时候，我们可能会想要修改那些对象。也许某些动作将需要触发状态改变，或者播放某种形式的动画。或者，我们可能想更改某些鼠标交互时的对象属性（颜色，不透明度，大小，位置）。 

Fabric负责我们的画布渲染和状态管理。我们只需要修改对象本身即可。

前面的示例演示了set方法以及如何调用`set({left：20，top：50})`从先前位置“移动”对象。以类似的方式，我们可以更改对象的任何其他属性。但是这些属性是什么？

好了，正如您所期望的，这些都与定位有关——left、top；尺寸——width、height；渲染——fill、opacity、stroke、strokeWidth；缩放和旋转——scaleX、scaleY、angle；甚至与翻转相关的内容——flipX、flipY和倾斜的skewX、skewY

是的，在Fabric中创建翻转对象就像将flip *属性设置为true一样容易。 

您可以通过get方法读取任何这些属性，并通过set进行设置。让我们尝试更改红色矩形的一些属性：

```vue
mounted(){
    var canvas = new fabric.Canvas("canvas",{backgroundColor:"grey"});
    var rect = new fabric.Rect({
      left:100,
      top:100,
      width:20,
      height:20,
    });
    canvas.add(rect);

    rect.set("fill","red");
    rect.set({strokeWidth:5,stroke:"rgba(100,200,200,0.5)"});
    rect.set("angle",60).set("flipY",true);
}
```

![image-20210319154452512](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319154452512.png)

首先，我们将“填充”值设置为“红色”，实质上使对象变成红色。下一条语句同时设置“ strokeWidth”和“ stroke”值，为矩形提供5px浅绿色的边框。最后，我们要更改“ angle”和“ flipY”属性。请注意，这3条语句中的每条语句如何使用，略有不同的语法。 

这证明了`set`是一种通用方法。您可能会经常使用它，因此它应该尽可能地方便。 

我们已经介绍了setter，那么getter呢？很明显，既有通用的`get`方法，也有许多特定的`get *`方法。要读取对象的“宽度”值，可以使用`get（'width'）`或`getWidth（）`。要获取“ scaleX”值，请使用`get（'scaleX'）`或`getScaleX（）`，依此类推。对于每个“公共”对象属性（例如“ stroke”，“ strokeWidth”，“ angle”等），都有一种类似于getWidth或getScaleX的方法。 

您可能会注意到，在先前的示例中，对象是使用与我们刚在`set`方法中使用过的配置相同的配置哈希值创建的。那是因为它是完全一样的。您可以在创建时“配置”对象，或在之后使用`set`方法：

```vue
mounted() {
    var canvas = new fabric.Canvas("canvas", { backgroundColor: "grey" });

    var rect = new fabric.Rect({
      width: 10,
      height: 20,
      fill: "#f55",
      opacity: 0.7,
    });

    // or functionally identical

    //var rect = new fabric.Rect();
    //rect.set({ width: 10, height: 20, fill: "#f55", opacity: 0.7 });

    canvas.add(rect);
 }
```

![image-20210319160446768](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319160446768.png)

#### Default options(默认参数)

此时，您可能会问——当我们创建一个对象而不传递任何“配置”对象时会发生什么。它仍然具有那些属性吗？

是的当然。 Fabric中的对象始终具有一组默认属性。在创建过程中被忽略时，将“赋予”对象的此默认属性集。我们可以尝试自己看看：

```vue
mounted() {
    var canvas = new fabric.Canvas("canvas", { backgroundColor: "grey" });

    var rect = new fabric.Rect()
    console.log(rect.get("width"));//0
    console.log(rect.get('height'));//0
    console.log(rect.get("left"));//0
    console.log(rect.get("top"));//0
    console.log(rect.get("fill"));//rgb(0,0,0)
    console.log(rect.get("stroke"));//null
    console.log(rect.get("opacity"));//1

    canvas.add(rect);
}
```

![image-20210319161246603](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319161246603.png)

我们的矩形具有一组默认属性。它的位置是(0,0)，是黑色，完全不透明，没有笔触，没有尺寸（宽度和高度均为0）。由于没有尺寸，因此我们无法在画布上看到它。但是给它任何宽度/高度的正值肯定会在画布的左/上角显示一个黑色的矩形。

```vue
mounted() {
    var canvas = new fabric.Canvas("canvas", { backgroundColor: "grey" });

    var rect = new fabric.Rect({width:20,height:20})
    console.log(rect.get("width"));//20
    console.log(rect.get('height'));//20
    console.log(rect.get("left"));//0
    console.log(rect.get("top"));//0
    console.log(rect.get("fill"));//rgb(0,0,0)
    console.log(rect.get("stroke"));//null
    console.log(rect.get("opacity"));//1

    canvas.add(rect);
}
```

![image-20210319161433016](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319161433016.png)

#### Hierarchy and Inheritance(层次结构和继承)

构造对象不仅彼此独立存在。它们形成了非常精确的层次结构。 

大多数对象都从根`fabric.Object`继承。 `fabric.Object`几乎代表一个二维形状，位于二维画布平面中。它是一个具有left / top和width / height属性以及一系列其他图形特征的实体。我们在对象上看到的那些属性（fill、stroke、angle、opacity、flip *等）对于所有从`fabric.Object`继承的Fabric对象都是共有的。 这种继承使我们可以在`fabric.Object`上定义方法，并在所有子“类”之间共享它们。例如，如果要在所有对象上都具有`getAngleInRadians`方法，则只需在`fabric.Object.prototype`上创建它：

```vue
mounted() {
    fabric.Object.prototype.getAngleInRadians = function(){
      return this.get("angle")/180*Math.PI;
    };

    var rect =new fabric.Rect({angle:45});
    console.log(rect.getAngleInRadians());//0.7853981633974483

    var circle = new fabric.Circle({angle:30,radius:10});
    console.log(circle.getAngleInRadians());//0.5235987755982988

    console.log(circle instanceof fabric.Circle);//true
    console.log(circle instanceof fabric.Object);//true
    }
```

如您所见，方法立即在所有实例上可用。 

虽然子“类”继承自`fabric.Object`，但它们通常还定义了自己的方法和属性。例如，`fabric.Circle`需要具有“半径”属性。稍后我们将要讨论的`fabric.Image`需要具有`getElement `/ `setElement`方法，以访问/设置图像实例所源自的HTML `<img>`元素。 

对于高级项目，使用原型来获取自定义渲染和行为是很常见的。

### Canvas(画布)

现在我们已经详细介绍了对象，让我们回到画布上。 

如果创建画布对象，则可以在所有Fabric示例中看到的第一件事——`new fabric.Canvas（'...'）`。 fabric.Canvas用作`<canvas>`元素的包装，并负责管理该特定画布上的所有Fabric对象。它接受元素的ID，并返回fabric.Canvas的实例。

我们可以在其上添加对象，从中引用或删除它们：

```vue
mounted(){
		var canvas = new fabric.Canvas('c');
var rect = new fabric.Rect();

//在canvas上添加一个对象
canvas.add(rect); 

//canvas上添加的第一个对象
canvas.item(0); 

//获取canvas上的所有对象
//这里需要注意，使用canvas.item或者canvas.item()获取所有对象是没有用的
canvas.getObjects(); 

//删除指定的对象
canvas.remove(rect); 
}
```

虽然管理对象是`fabric.Canvas`的主要目的，但它也可以用作配置画布本身。是否需要为整个画布设置背景颜色或图像？将所有内容都剪切到某个区域？设置不同的宽度/高度？指定画布是否是交互式的？所有这些选项（以及其他选项）都可以在创建时或之后在fabric.Canvas上设置：

```vue
mounted(){
		var canvas = new fabric.Canvas("canvas",{
      backgroundColor:'rgb(100,100,200)',
      selectionColor:'blue',
      selectionLineWidth:2
    })

    var rect = new fabric.Rect()
    canvas.add(rect);
}
```

![image-20210319171222435](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319171222435.png)

或者

```vue
mounted(){
		var canvas = new fabric.Canvas("canvas");
		canvas.setBackgroundColor("rgb(100,200,200)");
    canvas.onFpsUpdate = function(){
      console.log('success!');
    }

    var rect = new fabric.Rect();
    canvas.add(rect);
}
```

![image-20210319174149116](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319174149116.png)

#### Interactivity(互动性)

当我们讨论canvas元素时，让我们谈谈交互性。内置的Fabric的独特功能之一是，在我们刚刚查看的所有便捷对象模型之上，都具有一层交互性。 

存在对象模型以允许以编程方式访问和操纵画布上的对象。但是在外部，在用户级别上，有一种方法可以通过鼠标（或触摸设备上的触摸）来操纵这些对象。通过`new fabric.Canvas（'...'）`初始化画布后，就可以选择对象，对其进行拖动，缩放或旋转它们，甚至将它们组合在一起以在一个块中进行操作！

```vue
mounted(){
		var canvas = new fabric.Canvas("canvas");
		canvas.setBackgroundColor("rgb(100,200,200)");

    var rect = new fabric.Rect({width:20,height:20});
    canvas.add(rect);
}
```

原始画布如下：

![屏幕快照 2021-03-19 下午5.57.27](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202021-03-19%20%E4%B8%8B%E5%8D%885.57.27.png)

可以通过鼠标操作对象，如下：

![image-20210319180035162](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319180035162.png)

如果我们希望用户允许在画布上拖动某些东西（例如图像），我们要做的就是初始化画布并在其上添加一个对象。无需其他配置或设置。 

为了控制这种交互性，我们可以在画布上结合使用Fabric的“ selection”布尔属性和单个对象的“ selectable”布尔属性。

```vue
mounted(){
		var canvas = new fabric.Canvas("canvas");
    canvas.setBackgroundColor("rgb(100,200,200)");

    var rect = new fabric.Rect({width:20,height:20,left:100,top:50});
    var circle = new fabric.Circle({radius:30,fill:"rgb(100,100,200)"})

    //设置不能群体选择
    canvas.selection = false;
    //设置rect对象无法被选中
    rect.set("selectable",false);

    canvas.add(rect,circle);
}
```

这时候只有圆才能被选中操作。

![image-20210319180137348](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319180137348.png)

但是，如果您根本不想要这样的交互层，该怎么办？如果是这种情况，您始终可以用`fabric.StaticCanvas`替换`fabric.Canvas`。初始化的语法完全相同。您只能使用`StaticCanvas`而不是`Canvas`。

```vue
mounted(){
		var staticCanvas = new fabric.StaticCanvas("canvas");

    staticCanvas.setBackgroundColor("rgb(100,200,200)");

    var rect = new fabric.Rect({width:20,height:20,left:100,top:50});
    var circle = new fabric.Circle({radius:30,fill:"rgb(100,100,200)"})

    staticCanvas.add(rect,circle);
}
```

![image-20210319180927556](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319180927556.png)

这将创建一个“较轻”版本的画布，而没有任何事件处理逻辑。请注意，您仍然可以使用整个对象模型-添加对象，删除或修改它们，以及更改任何画布配置-所有这些仍然有效。只是事件处理不见了。 

稍后，当我们遍历自定义构建选项时，您会看到，如果只需要`StaticCanvas`，您甚至可以创建一个轻量级的Fabric。如果您需要非交互式图表或应用程序中带有过滤器的非交互式图像，这可能是一个不错的选择。

### Images(图像)

说起图片… 在画布上添加矩形和圆形很有趣，但是为什么我们不玩一些图像呢？就像您现在想像的那样，Fabric使这变得容易。让我们实例化`fabric.Image`对象并将其添加到画布：

```vue
<template>
  <div class="app">
    <canvas id="canvas" width="400" height="300" ></canvas>
    <img src="../src/assets/logo.png" alt="picture" id="img" width="100" height="auto" >
  </div>
</template>

<script>
import { fabric } from "fabric";
export default {
  mounted() {
   	var canvas = new fabric.Canvas("canvas");
    var imgElement = document.getElementById("img");

    var imgInstance = new fabric.Image(imgElement,{
      left:100,
      top:10,
      angle:30,
      opacity:0.85
    });
    canvas.add(imgInstance);
  },
};
</script>

<style>
</style>
```

注意我们如何将图像元素传递给`fabric.Image`构造函数。这将创建一个`fabric.Image`实例，其外观与文档中的图像相似。此外，我们立即将left/top设置为100/10，角度设置为30，不透明度设置为0.85。一旦添加到画布上，图像将在100,10位置，30度角处渲染，并且稍微透明！不错。

![image-20210319184929008](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210319184929008.png)

现在，如果我们在文档中没有真正的图像，而只是图像的URL，该怎么办？没问题让我们看看如何使用`fabric.Image.fromURL`:

```vue
<template>
  <div class="imageFilter">
      <canvas id="canvas" width="300" height="300"></canvas>
  </div>
</template>

<script>
import {fabric} from "fabric"
export default {
    mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:'#ddd'})

        fabric.Image.fromURL("https://ss0.baidu.com/-Po3dSag_xI4khGko9WTAnF6hhy/zhidao/wh=450,600/sign=093b8529572c11dfde84b72756174ee6/aa18972bd40735fa8d2f0c739e510fb30e240818.jpg",(img)=>{
            canvas.add(img);
        })
    }

}
</script>


<style>

</style>
```

![image-20210320155638670](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320155638670.png)

看起来很简单，不是吗？只需使用图像的URL调用`fabric.Image.fromURL`，然后给它一个回调即可在加载和创建图像后调用。回调函数接收已创建的`fabric.Image`对象作为第一个参数。此时，您可以将其添加到画布，或者可能先更改，然后再添加到画布：

```vue
mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:'#ddd'})

        fabric.Image.fromURL("https://ss0.baidu.com/-Po3dSag_xI4khGko9WTAnF6hhy/zhidao/wh=450,600/sign=093b8529572c11dfde84b72756174ee6/aa18972bd40735fa8d2f0c739e510fb30e240818.jpg",(img)=>{
      img.scale(0.5).set("flipX",true)
            canvas.add(img);
        })
    }
```

![image-20210320155726595](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320155726595.png)

### Paths

我们研究了简单的形状，然后是图像。但是更复杂，更丰富的形状和内容呢？ 

结识实力雄厚的一对——路径和群组。 

Fabric中的路径代表可以用其他方式填充，描边和修改的形状轮廓。路径由一系列命令组成，这些命令实际上模仿了笔从一个点到另一个点的过程。借助“移动”，“线”，“曲线”或“弧”等命令，路径可以形成难以置信的复杂形状。在路径组（PathGroup's）的帮助下，可能性更大。 

Fabric中的路径非常类似于SVG `<path>`元素。它们使用相同的命令集，可以从`<path>`元素创建它们，并将其序列化。稍后我们将更仔细地研究序列化和SVG解析，但是现在值得一提的是，您可能很少会手动创建Path实例。相反，您将使用Fabric的内置SVG解析器。但是要了解什么是Path对象，让我们尝试手动创建一个简单的对象：

```vue
<template>
  <div class="app">
    <canvas id="canvas" width="400" height="400" ></canvas>
  </div>
</template>

<script>
import { fabric } from "fabric";
export default {
  mounted() {
    var canvas = new fabric.Canvas("canvas",{backgroundColor:"grey"});
    var path = new fabric.Path("M 0 0 L 200 100 L 170 200 z");
    path.set({left:120,top:120});
    canvas.add(path);
  },
};
</script>

<style>
</style>
```

![image-20210320100814266](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320100814266.png)

我们正在实例化`fabric.Path对`象，并向其传递一串路径指令。虽然看起来很神秘，但实际上很容易理解。 “ M”表示“移动”命令，并指示隐形笔移动到(0、0)点。 “ L”代表“线”，使笔画一条线到(200、100)点。然后，另一个“ L”创建一条到(170、200)的线。最后，“ z”告诉强制绘图笔关闭电流路径并最终确定形状。结果，我们得到一个三角形。 

由于`fabric.Path`就像Fabric中的任何其他对象一样，我们也能够更改其某些属性。但是我们可以对其进行更多修改：

```vue
mounted() {
   var canvas = new fabric.Canvas("canvas",{backgroundColor:"#ddd"});
    var path = new fabric.Path("M 0 0 L 300 100 L 200 300 z");
    path.set({ fill: "red", stroke: "green", opacity: 0.5 });
    canvas.add(path);
  }
```

![image-20210320101536800](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320101536800.png)

出于好奇，让我们看一下稍微复杂一些的路径语法。您将看到为什么手动创建路径可能不是最好的主意。

```vue
mounted(){
		var canvas = new fabric.Canvas("canvas",{backgroundColor:"#ddd"});
  
    var path = new fabric.Path('M121.32,0L44.58,0C36.67,0,29.5,3.22,24.31,8.41\
c-5.19,5.19-8.41,12.37-8.41,20.28c0,15.82,12.87,28.69,28.69,28.69c0,0,4.4,\
0,7.48,0C36.66,72.78,8.4,101.04,8.4,101.04C2.98,106.45,0,113.66,0,121.32\
c0,7.66,2.98,14.87,8.4,20.29l0,0c5.42,5.42,12.62,8.4,20.28,8.4c7.66,0,14.87\
-2.98,20.29-8.4c0,0,28.26-28.25,43.66-43.66c0,3.08,0,7.48,0,7.48c0,15.82,\
12.87,28.69,28.69,28.69c7.66,0,14.87-2.99,20.29-8.4c5.42-5.42,8.4-12.62,8.4\
-20.28l0-76.74c0-7.66-2.98-14.87-8.4-20.29C136.19,2.98,128.98,0,121.32,0z');

canvas.add(path.set({ left: 100, top: 200 }));
}
```

哦，孩子，这是怎么回事？ 

嗯，“ M”仍然代表“移动”命令，因此笔在“ 121.32，0”点开始绘制过程。然后是“ L”命令，将其设置为“ 44.58，0”。到目前为止，一切都很好。下一步是什么？ “ C”命令，代表“三次贝塞尔曲线”。它使笔绘制贝塞尔曲线从当前点到“ 36.67，0”之一。它以“ 29.5，3.22”作为控制点在行的开头，并以“ 24.31，8.41”作为控制点在行的末尾。然后，接下来是许多其他三次贝塞尔曲线命令，这些命令最终创建了一个漂亮的箭头形状。

很有可能，您不会直接使用此类“野兽”。相反，您可能想使用`fabric.loadSVGFromString`或`fabric.loadSVGFromURL`方法之类的方法来加载整个SVG文件，并让Fabric的SVG解析器完成遍历所有SVG元素并创建相应的Path对象的工作。 

说到整个SVG文档，虽然Fabric的Path通常代表SVG `<path>`元素，但是SVG文档中经常出现的一组路径被表示为Groups（`fabric.Group`实例）。可以想象，Group只是一组Paths和其他对象。而且由于`fabric.Group`继承自`fabric.Object`，因此可以像其他任何对象一样将其添加到画布，并以相同的方式进行操作。 

就像使用Paths一样，您可能不会直接使用它们。但是，如果您在解析SVG文档后偶然发现一个文档，您将确切知道它是什么以及它的作用。

### Afterword(后记)

我们仅介绍了Fabric可能实现的功能。现在，您可以轻松创建任何简单的形状，复杂的形状，图像；将它们添加到画布上，然后以所需的任何方式进行修改（positions、dimensions、angles、colors、strokes、opacity），即可为其命名。 

在本系列的下一部分中，我们将研究与groups的合作。动画片;文本; SVG解析，渲染，序列化；事件;图像滤镜；和更多。 

同时，请随时查看[带注释的演示](http://fabricjs.com/demos/)或[基准](http://fabricjs.com/benchmarks/)，加入[Google小组](https://groups.google.com/forum/?fromgroups#!forum/fabricjs)或[其他地方的讨论](http://stackoverflow.com/questions/tagged/fabricjs)，或者直接获取[文档](http://fabricjs.com/docs/)，[Wiki](https://github.com/kangax/fabric.js/wiki)和[源代码](https://github.com/kangax/fabric.js)。 尝试使用Fabric玩得开心！

希望您喜欢。

阅读[Part 2](http://fabricjs.com/fabric-intro-part-2)/[第二部分](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js.%20Part%202.md)。