# Introduction to Fabric.js. Part 2

在本系列的[第一部分中](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js:%20Part%201.md)，我们只是开始熟悉Fabric.js。我们研究了使用Fabric的原因，对象模型和对象层次结构，Fabric中可用的不同种类的实体-简单的形状，图像和复杂的路径。我们还学习了如何对画布上的Fabric对象执行简单的操作。 

既然大多数基本知识都结束了，那么让我们开始一些有趣的事情吧！

### Animation

没有动画工具的canvas库是不会受人尊重的。而且Fabric也不例外。由于具有如此强大的对象模型和图形功能，因此如果没有内置动画助手将是很可惜的。 

还记得更改任何对象的属性有多么容易吗？我们只是调用了`set`方法，并传递了相应的值：

```js
rect.set("angle",45)
```

好吧，为对象设置动画同样容易。每个Fabric对象都有一个`animate`方法，该方法可以动画化该对象。

```vue
<template>
  <div class="animate">
      <canvas id="canvas" width="300" height="300"></canvas>
  </div>
</template>

<script>
import {fabric} from "fabric"
export default {
    mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:"#ddd"})
        var rect = new fabric.Rect({width:100,height:50});
        
        rect.animate("angle",45,{
            onChange:canvas.renderAll.bind(canvas)
        })
        
        canvas.add(rect)
    }
}
</script>

<style>

</style>
```

![gif3](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/gif3.gif)

第一个参数是要设置动画的属性。第二个参数是动画的结束值。如果矩形具有-15°的角度，并且我们传递了45，则动画将从-15°变为45°。第三个参数是一个可选对象，用于指定动画的详细信息-持续时间，回调，缓动等。

`animate`的一个便利功能是它还支持相对值。例如，如果您想为对象的left属性设置100px的动画，则可以这样做：

```js
mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:"#ddd"})
        var rect = new fabric.Rect({width:100,height:50});
        
        rect.animate("left","+=100",{onChange:canvas.renderAll.bind(canvas)});
        
        canvas.add(rect)
    }
```

![gif2](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/gif2.gif)

同样，可以将对象逆时针旋转5度，如下所示：

同样，可以将对象逆时针旋转5度，如下所示：

```js
mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:"#ddd"})
        var rect = new fabric.Rect({width:100,height:50});

        rect.animate("angle","-5",{onChange:canvas.renderAll.bind(canvas)});
        canvas.add(rect)
    }
```

![gif1](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/gif1.gif)

您可能想知道为什么我们总是在此处指定“ onChange”回调。第三个参数不是可选的吗？是的，但是在每个动画帧上调用`canvas.renderAll`才使我们能够看到实际的动画！您会看到，当我们调用`animate`方法时，它只会按照特定算法（即缓动）随时间对属性值进行动画处理。因此，rect.animate（'angle'，45）将更改对象的角度，但在每次更改角度后都不会重新渲染画布屏幕。我们显然需要重新渲染才能看到实际的动画。 

您可能想知道为什么我们总是在此处指定“ onChange”回调。第三个参数不是可选的吗？是的，但是在每个动画帧上调用`canvas.renderAll`才使我们能够看到实际的动画！您会看到，当我们调用`animate`方法时，它只会按照特定算法（即缓动）随时间对属性值进行动画处理。因此，`rect.animate（'angle'，45）`将更改对象的角度，但在每次更改角度后都不会重新渲染画布屏幕。我们显然需要重新渲染才能看到实际的动画。

请记住，在画布表面下方有整个对象模型。对象具有自己的属性和关系，而画布仅负责将它们的存在投射到外部世界。 

每次更改后动画没有自动重新渲染画布的原因是由于性能。毕竟，我们可以在画布上有成百上千个动画对象，如果每个对象都试图重新渲染屏幕并不是很好。对于许多对象，您可以使用诸如`requestAnimationFrame`（或其他基于计时器的）循环之类的东西连续不断地渲染画布，而无需为每个对象调用`renderAll`。但是大多数时候，您可能需要显式指定`canvas.renderAll`作为“ onChange”回调。

那么我们可以传递哪些其他选项进行动画处理呢？

- from：允许指定可设置动画的属性的起始值（如果我们不希望使用当前值）。 

- duration：默认为500（ms）。可用于更改动画的持续时间。 
- onComplete：在动画结束时调用的回调。 
- easing：缓动功能。

所有这些选择都应该是不言自明的，除非可能是一个简单的选择。让我们仔细看看。

默认情况下，`animate`使用“ easeInSine”功能进行动画处理。如果这不是您所需要的，那么`fabric.util.ease`下提供了许多宽松选项。例如，如果我们想以弹性的方式将对象向右移动：

```js
rect.animate("left",500,{
  onChange:canvas.renderAll.bind(canvas),
  duration:3000,
  easing:fabric.util.ease.easeOutBounce
});
```

![gif4](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/gif4.gif)

因此，这涵盖了Fabric的动画部分。只是给您一些可能的想法-您可以为对象的角度设置动画以使其旋转。动画左/上属性以使其移动；动画宽度/高度，使其缩小/增长；为不透明度设置动画以使其淡入/淡出；等等。

### Image filters(图像滤镜)

在本系列的第一部分中，我们学习了如何在Fabric中使用图像。有`fabric.Image`构造函数，它接受图像元素。还有`fabric.Image.fromURL`方法，它可以创建URL字符串的图像实例。这些图像中的任何一个都可以像其他任何对象一样抛出并呈现在画布上。 

但是，尽管处理图像非常有趣，但对它们应用图像滤镜甚至更酷！ 

默认情况下，Fabric对于启用WEBGL的浏览器均不提供很少的筛选器。这也使定义自己的过程变得容易。您可能非常熟悉的一些内置滤镜-滤镜可去除白色背景，灰度滤镜，反转或亮度滤镜。其他一些可能不那么受欢迎-彩色矩阵，棕褐色或噪点。 

那么我们如何将滤镜应用于Fabric中的图像？好吧，`fabric.Image`的每个实例都具有“ filters”属性，该属性是一个简单的过滤器数组。该阵列中的每个过滤器都是Fabric过滤器之一的实例。或您自己的自定义过滤器的实例。

因此，让我们创建一个灰度图像。

```vue
<template>
  <div class="imageFilter">
      <canvas id="canvas" width="500" height="500"></canvas>
  </div>
</template>

<script>
import {fabric} from "fabric"
export default {
    mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:'#ddd'})
				let imgUrl = "..."
        fabric.Image.fromURL(imgUrl,(img)=>{
            img.filters.push(new fabric.Image.filters.Grayscale())
            img.applyFilters();
            // img.scale(0.5).set("flipX",true)
            canvas.add(img);
        })
    }
}
</script>


<style>

</style>
```

（说明：这里图片可能会出错，我放本地图片地址会报“Cannot read property 'naturalWidth' of null”的错误，直接放网络图片地址会报“Failed to execute 'texImage2D' on 'WebGLRenderingContext': The image element contains cross-origin data, and may not be loaded.”的错误。

我采用了[这里](https://www.jq22.com/web-skill78)的解决方法，将本地图片转为Base64格式，因此上面代码`let imgUrl = “...”`的引号中是一段Base64编码。以下其他代码同。）

![image-20210320160751821](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320160751821.png)

图像的棕褐色版本如何？

```vue
mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:'#ddd'})
				let imgUrl = "..."
        fabric.Image.fromURL(imgUrl,(img)=>{
            img.filters.push(new fabric.Image.filters.Sepia());
            img.applyFilters();
            canvas.add(img);
        })
    }
```



![image-20210320163029438](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320163029438.png)

由于“filters”属性是一个简单的数组，因此我们可以用通常的方式对其执行任何所需的操作-删除过滤器（通过`pop`，`splice`或`shift`），添加过滤器（通过`push`，`splice`或`unshift`），甚至组合多个过滤器。当我们调用`applyFilters`时，“过滤器”数组中存在的所有过滤器都会被一一应用。因此，让我们尝试创建既棕褐色又明亮的图像。

```vue
mounted(){
        var canvas = new fabric.Canvas("canvas",{backgroundColor:'#ddd'})
				let imgUrl = "..."
        fabric.Image.fromURL(imgUrl,(img)=>{
            img.filters.push(
                new fabric.Image.filters.Sepia(),
                new fabric.Image.filters.Brightness({brightness:0.5})
                );
            img.applyFilters();
            canvas.add(img);
        })
    }
```



![image-20210320164727541](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320164727541.png)

请注意，我们还将`{Brightness：100}`（官方教程是100，而我这里这里只给到了0.5，可能是图片原因又活着是其他，我给到1就全白了。）对象传递给了Brightness滤镜。这是因为可以应用某些过滤器而无需任何其他配置（例如，grayscale，invert，sepia），而其他过滤器可以对其行为进行更好的控制。对于亮度滤镜，它是实际的亮度级别（-1 全黑到1 全白）。对于噪声滤波器，它是噪声值（0-1000）。对于“去除颜色”滤镜，它是阈值和距离值。等等。

因此，既然您熟悉了Fabric过滤器，就该打破常规，创建自己的过滤器了！ 

用于创建过滤器的模板非常简单。我们需要创建一个“类”，然后定义`applyTo`方法。 （可选）我们可以将filter赋予`toJSON`方法（支持JSON序列化）和/或`initialize`方法（支持可选参数）。

```js
mounted(){
	var canvas = new fabric.Canvas("canvas",{backgroundColor:'#ddd'})
				let imgUrl = "...";

fabric.Image.filters.Redify = fabric.util.createClass(
      fabric.Image.filters.BaseFilter,
      {
        type: "Redify",

        /**
         * Fragment source for the redify program
         */

        fragmentSource:
          "precision highp float;\n" +
          "uniform sampler2D uTexture;\n" +
          "varying vec2 vTexCoord;\n" +
          "void main() {\n" +
          "vec4 color = texture2D(uTexture, vTexCoord);\n" +
          "color.g = 0.0;\n" +
          "color.b = 0.0;\n" +
          "gl_FragColor = color;\n" +
          "}",

        applyTo2d: function (options) {
          var imageData = options.imageData,
            data = imageData.data,
            i,
            len = data.length;
          for (i = 0; i < len; i += 4) {
            data[i + 1] = 0;
            data[i + 2] = 0;
          }
        },
      }
    );
    fabric.Image.filters.Redify.fromObject =
      fabric.Image.filters.BaseFilter.fromObject;
                              
   fabric.Image.fromURL(imgUrl, (img) => {
      img.filters.push(new fabric.Image.filters.Redify());
      img.applyFilters();
      canvas.add(img);

   })
}
```



![image-20210320172351760](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210320172351760.png)

在不花太多时间研究此代码的情况下，主要动作是循环循环。其中我们将每个像素的绿色（data [i + 1]）和蓝色（data [i + 2]）分量替换为0，从本质上将它们删除。标准rgb三元组的红色部分保持不变，基本上使整个图像都涂成红色。如您所见，正在向`applyTo`方法传递一个选项对象，该对象包含在筛选管道阶段的图像的imageData。从那里，我们可以遍历其像素（`getImageData().data`），以我们想要的任何方式对其进行修改。如果浏览器是启用WEBGL的过滤器，则可以在GPU上运行。为此，您必须提供一个片段着色器，以描述对像素执行的操作。构造中定义了许多过滤器，您可以在其中看到如何编写片段或顶点着色器的示例。

### Colors

无论您是使用十六进制，RGB还是RGBA颜色更舒适，Fabric均可提供坚实的色彩基础，以帮助您最自然地表达自己。以下是在Fabric中定义颜色的一些方法：

```js
new fabric.Color('#f55');
new fabric.Color('#123123');
new fabric.Color('356735');
new fabric.Color('rgb(100,0,100)');
new fabric.Color('rgba(10, 20, 30, 0.5)');
```

转换也很简单。 toHex()会将颜色实例转换为十六进制表示形式。 toRgb()—变为RGB one，toRgba()—变为具有alpha通道的RGB。

```js
new fabric.Color('#f55').toRgb(); // "rgb(255,85,85)"
new fabric.Color('rgb(100,100,100)').toHex(); // "646464"
new fabric.Color('fff').toHex(); // "FFFFFF"
```

转换不是唯一可以使用颜色进行的操作。您还可以将一种颜色与另一种颜色叠加，或将其转换为灰度版本。

```js
var redish = new fabric.Color('#f55');
var greenish = new fabric.Color('#5f5');

redish.overlayWith(greenish).toHex(); // "AAAA55"
redish.toGrayscale().toHex(); // "A1A1A1"
```

### Gradients

使用颜色的一种更具表现力的方式是通过渐变。渐变使我们可以将一种颜色混合到另一种颜色，从而创建一些令人惊叹的图形效果。

Fabric支持在所有对象上设置填充或描边属性的渐变。为了为对象设置渐变，请首先创建渐变，然后将其分配给填充或描边。

```vue
<template>
  <div class="test">
      <canvas id="canvas"></canvas>
  </div>
</template>

<script>
import {fabric} from "fabric"
export default {
    data(){
        return {
            canvas:''
        }
    },
    mounted(){
    this.canvas = new fabric.Canvas("canvas",{
        width:300,
        height:300,
        backgroundColor:"#ddd"
    })
      
    var circle = new fabric.Circle({
        left:100,
        top:100,
        radius:50
    })

    var gradient = new fabric.Gradient({
         type:"linear",
         gradientUnits:"pixels",
         coords:{x1:0,y1:0,x2:0,y2:circle.height},
         colorStops:[
             {offset:0,color:"#000",},
             {offset:1,color:"#fff"}
             ] 
    })
    circle.set("fill",gradient)
    this.canvas.add(circle)
    },
}
</script>

<style>

</style>
```

![image-20210323163018124](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323163018124.png)

在上面的示例中，我们在100,100位置创建了一个半径为50px的圆。然后，我们将其填充设置为一个渐变，该渐变跨越该圆圈的整个高度，从黑色到白色。

渐变选项对象具有2个主要属性，coords和colorStops。`coords`期望至少2个坐标对（x1，y1和x2，y2）将定义渐变在对象上的扩展方式，而`colorStops`是定义渐变颜色的数组。数组中的每种颜色都由一个`offset`定义，该偏移量表示其在渐变上的位置，该`color`定义了颜色本身，并最终定义了`opacity`属性。您可以定义任意多个色标，只要它们的偏移范围为0到1。 “ 0”代表渐变的开始，“ 1”代表渐变的结束。坐标是相对于对象左上角的，因此圆的最高点是0，最低点是`circle.height`。

您可以指定类型为`linear`或`radial`以获得两种不同类型的渐变，而且`gradientUnits`默认为像素，但可以指定为“百分比”。 “百分比”将允许以对象大小的百分比指定渐变大小，“ 1”是对象大小的100％。

此设置对`fabric.Text`对象有用，该对象根据文本内容更改宽度或高度。

这是一个从左到右的红蓝色渐变的示例：

```vue
 mounted(){
    this.canvas = new fabric.Canvas("canvas",{
        width:300,
        height:300,
        backgroundColor:"#ddd"
    })
      
    var circle = new fabric.Circle({
        left:100,
        top:100,
        radius:50
    })

 			var gradient1 = new fabric.Gradient({
         type:"linear",
         gradientUnits:"pixels",//or "pencentage"
         coords:{x1:0,y1:0,x2:circle.height,y2:0},
         colorStops:[
             {offset:0,color:"red"},
             {offset:1,color:"blue"}
         ]
     })

    // or in pencentage
    // var gradient2 = new fabric.Gradient({
    //     type:"linear",
    //     gradientUnits:"percentage",
    //     coords:{x1:0,y1:0,x2:1,y2:0},
    //     colorStops:[
    //         {offset:0,color:'red'},
    //         {offset:1,color:"blue"}
    //     ]
    // })

    circle.set("fill",gradient1)
    this.canvas.add(circle)
    },
```

![image-20210323163343907](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323163343907.png)

这是一个5级彩虹渐变，颜色的间隔甚至为20％：

 mounted(){
    this.canvas = new fabric.Canvas("canvas",{
        width:300,
        height:300,
        backgroundColor:"#ddd"
    })
      

```vue
var circle = new fabric.Circle({
    left:100,
    top:100,
    radius:50
})


    var gradient3 = new fabric.Gradient({
        type:"linear",
        gradientUnits:"pixels",
        coords:{x1:0,y1:0,x2:circle.height,y2:0},
        colorStops:[
            {offset:0,color:'red'},
            {offset:0.2,color:'orange'},
            {offset:0.4,color:'yellow'},
            {offset:0.6,color:"green"},
            {offset:0.8,color:'blue'},
            {offset:1,color:"purple"}
        ]
    })

circle.set("fill",gradient3)
this.canvas.add(circle)
},
```
![image-20210323163501501](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323163501501.png)

### Text

如果您不仅想在画布上显示图像和矢量形状，还想显示文本怎么办？Fabric都为你做好了！遇到`fabric.Text`对象。

我们在Fabric中提供文本抽象有两个原因。首先，允许以面向对象的方式处理文本。原生canvas方法-像往常一样-只允许进行低级别的填充或笔划文本。通过实例化`fabric.Text`例如，我们可以像处理任何其他Fabric object一样处理文本—移动它、缩放它、更改它的属性等等。

第二个原因是要提供比canvas所提供的功能更丰富的功能。某些Fabric附加功能包括：

- **Multiline support** 不幸的是，原生文本方法只是忽略了新行。
- **Text alignment** Left, center, right. 使用多行文字时非常有用。
- **Text background** 背景也遵循文本对齐。
- **Text decoration** Underline, overline, strike-through.
- **Line height** 使用多行文字时出错。
- **Char spacing** 使文本更紧凑或间距更大。
- **Subranges** 将颜色和属性应用于文本对象的子范围。
- **Multibyte** 支持表情！
- **On canvas editing** 使用交互式类，您可以直接在canvas上键入文本。

hello word的例子怎么样？

```js
var text = new fabric.Text('hello world', { left: 100, top: 100 });
canvas.add(text);
```

这是正确的！在画布上显示文本就像在所需位置添加`fabric.Text`实例一样简单。如您所见，唯一需要的第一个参数是实际的文本字符串。第二个参数是通常的选项对象，可以具有任何通常的left，top，fill，opacity等属性。

但是，当然，文本对象也具有其自己的，独特的，与文本相关的属性。让我们看看其中的一些：

#### fontFamily

默认情况下设置为“ Times New Roman”，此属性允许我们更改用于渲染文本对象的字体系列。更改它会立即使文本以新字体呈现。

#### fontWeight

字体粗细允许使文本看起来更粗或更细。就像在CSS中一样，您可以使用关键字（“ normal”，“ bold”）或数字（100、200、400、600、800）。请注意，是否可以使用特定的粗细取决于所选字体的粗细可用性。如果您使用的是远程字体，则需要确保同时提供普通和粗体（以及任何其他必需的粗细）字体定义。

```vue
<template>
  <div class="text">
      <canvas id="canvas"></canvas>
  </div>
</template>
<script>
import {fabric} from "fabric"
export default {
    data(){
        return {
            canvas:''
        }
    },
    mounted(){
        this.canvas =new fabric.Canvas("canvas",{
            width:400,
            height:300,
            backgroundColor:'#ddd'
        })

        var text = new  fabric.Text("hello world",{
            top:5
        })

        var comicSansText = new fabric.Text("I am in Comic Sans",{
            fontFamily:"Comic Sans",
            top:45
        })

        var text40  = new fabric.Text("I am at fontsize 40",{
            fontSize:40,
            top:105
        })

        var text20 = new fabric.Text("I am at fontSize 20",{
            fontSize:20,
            top:145
        })

        var normalText = new fabric.Text("I am a normal text",{
            fontWeight:"normal",
            top:185
        })

        var boldText = new fabric.Text("I am at bold text",{
            fontWeight:"bold",
            top:225
        })
   this.canvas.add(text,comicSansText,text40,text20,normalText,boldText)
    }
}
</script>

<style>
</style>
```

![image-20210323180154147](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323180154147.png)

#### textDecoration

文本修饰允许在文本上添加非轮廓，上划线或删除线。这与CSS相似，但是Fabric更进一步，并允许将以上内容的任何组合一起使用。因此，您可以同时使用带下划线和上划线，或带下划线和删除线的文本。

#### shadow

在1.3.0版之前，此属性称为“ textShadow” 。

文字阴影由4个成分组成：颜色，水平偏移，垂直偏移和模糊大小。如果您使用过CSS中的阴影，这可能看起来非常熟悉。通过更改这些值，可以进行很多组合。

```vue
 mounted(){
        this.canvas =new fabric.Canvas("canvas",{
            width:400,
            height:400,
            backgroundColor:'#ddd'
        })

        var underlineText = new fabric.Text("I am an undrline text",{
            underline:true,
            top:5
        })

        var strokeThroughText = new fabric.Text("I am a stroke-through text",{
            linethrough:true,
            top:65
        })

        var overlineText = new fabric.Text("I am overline text",{
            overline:true,
            top:125
        })

        var shadowText1 = new fabric.Text("I am a text with shadow",{
            shadow:"rgba(0,0,0,0.3) 5px 5px 5px",
            top:185

        })

        var shadowText2 = new fabric.Text("And another shadow",{
            shadow:"rgba(0,0,0,0.2) 0 0 5px",
            top:245
        })

        var shadowText3 = new fabric.Text("Lorem ipsum dolor sit",{
            shadow:"green -5px -5px 3px",
            top:305
        })
       this.canvas.add(underlineText,strokeThroughText,overlineText,shadowText1,shadowText2,shadowText3)
    }
```

![image-20210323181512563](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323181512563.png)

#### fontStyle

字体样式可以是2个值之一：normal或italic。这类似于同名的CSS属性。

#### stroke and strokeWidth

通过组合笔触（笔触的颜色）和strokeWidth（其宽度），可以对文本实现一些有趣的效果。

#### textAlign

使用多行文本时，文本对齐很有用。对于单行文本，边框的宽度始终与该行的宽度完全匹配，因此没有任何对齐的地方。

允许的值为“ left”，“ center”和“ right”。

```vue
 mounted(){
        this.canvas =new fabric.Canvas("canvas",{
            width:500,
            height:400,
            backgroundColor:'#ddd'
        })

        var italicText = new fabric.Text("A very fancy italic text",{
            fontStyle:'italic',
            fontFamily:"Delicious",
            top:5
        })

        var anotherItalicText = new fabric.Text("another italic text",{
            fontStyle:"italic",
            fontFamily:"Hoefler Text",
            top:65
        })

        var textWithStroke = new fabric.Text("Text with a stroke",{
            stroke:"#ff1318",
            strokeWidth:1,
            top:145
        })

        var loremIpsumDolor = new fabric.Text("Lorem ipsum dolor",{
            fontFamily:"Impact",
            stroke:"#c3bfbf",
            strokeWidth:3,
            top:205
        })

        var text = "this is\na mutiline\nnext\naligned right!";
        var alignedRightText = new fabric.Text(text,{
            textAlign:"right",
            top:285
        }) 
        this.canvas.add(italicText,anotherItalicText,textWithStroke,loremIpsumDolor,alignedRightText)
    }
```

![image-20210323183857162](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323183857162.png)

#### lineHeight

CSS领域可能熟悉的另一个属性是lineHeight。它允许我们更改多行文本中文本行之间的垂直间距。在下面的示例中，第一个文本块的lineHeight为3，第二个文本为— 1。

#### textBackgroundColor

最后，textBackgroundColor可以为文本提供背景。请注意，背景仅填充文本字符占用的空间，而不填充整个边界框。这意味着文本对齐方式会更改文本背景的呈现方式。线高也是如此，因为背景尊重线之间的垂直空间，由lineHeight创建。

```vue
mounted(){
        this.canvas =new fabric.Canvas("canvas",{
            width:600,
            height:1000,
            backgroundColor:'#ddd'
        })

        var text2 = "Lorem ipsum dolor sit amet,\nconsectetur adipisicing elit,\nsed do eiusmod tempor incididunt\nut labo"
        var lineHeight2 = new fabric.Text(text2,{
            lineHeight:2,
            top:5
        })
        var lineHeight1 = new fabric.Text(text2,{
            lineHeight:1,
            top:345
        })

        var text3 = "this is\na multiline\ntext\nwith\ncustom lineheight\n& backgound!"
        var textWithBackground = new fabric.Text(text3,{
            textBackgroundColor:"rgb(0,200,0)",
            top:565
        })
        this.canvas.add(lineHeight2,lineHeight1,textWithBackground)
    }
```

![image-20210323185412415](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323185412415.png)

![image-20210323185503786](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323185503786.png)

### Events

事件驱动的体系结构是框架内强大功能和灵活性的基础。 Fabric也不例外，它提供了一个广泛的事件系统，从低级的“鼠标”事件到高级的对象事件。

这些事件使我们能够利用画布上发生的各种动作的不同时刻。是否想知道何时按下鼠标？只需观察“ mouse:down”事件即可。将对象添加到画布的时间怎么样？ “object:added”在那里为您服务。那么当整个画布重新渲染时又如何呢？只需使用“ after:render”。

事件API非常简单，类似于jQuery，Underscore.js或其他流行的JS库。有`on`方法可以初始化事件监听器，而`off`方法可以将其删除。

让我们看一个实际的例子：

```vue
<template>
  <div class="event">
    <canvas id="canvas"></canvas>
  </div>
</template>
<script>
import { fabric } from "fabric";
export default {
  mounted() {
    var canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

    canvas.on("mouse:down", (options) => {
      console.log(options.e.clientX, options.e.clientY);
    });
  },
};
</script>
<style>
</style>
```

![image-20210323192522423](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323192522423.png)

我们将事件“ mouse:down”事件侦听器添加到画布上，并为其提供事件处理程序，该事件处理程序将记录事件起源的坐标。换句话说，它将记录鼠标在画布上的确切位置。事件处理程序接收一个options对象，该对象具有2个属性：e-原始事件，target-画布上单击的对象（如果有）。该事件始终存在，但仅当您确实单击画布上的某个对象时，目标才存在。目标也仅传递给有意义的事件处理程序。例如，对于“ mouse:down”而不是“ after:render”（表示重新绘制了整个画布）。

```js
  mounted() {
    var canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });
    
        var rect = new fabric.Rect({
        width:100,
        height:50,
        fill:"green"
    })
    rect.on("selected",()=>{
        console.log('selected a rectangle');
    })

    canvas.on("mouse:down", (options) => {
      console.log(options.e.clientX, options.e.clientY);
      if (options.target) {
        console.log("an object was clicked!", options.target.type);
      }
    });
    
    canvas.add(rect)
  }
```

![image-20210323192839388](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323192839388.png)

![image-20210323192804078](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323192804078.png)

上面的示例将记录“单击了一个对象！”如果单击一个对象。它还将显示单击的对象的类型。

那么Fabric中还有哪些其他事件可用？好吧，在鼠标级别的菜单中有“ **mouse:down**”，“ **mouse:move**”和“ **mouse:up**”。从通用的开始，有“ **after:render**”。然后是与选择相关的事件：“**before:selection:cleared**”，“**selection:created**”，“**selection:cleared**”。最后，对象是：“**object:modified**”，“**object:selected**”，“**object:moving**”，“**object:scaling**”，“**object:rotating**”，“**object:added**”和“**object:removed**”

请注意，每次移动（或缩放）一个对象（甚至移动一个像素）时，都会连续触发“ object:moving”（或“ object:scaling”）之类的事件。另一方面，仅在操作（对象修改或选择创建）结束时才触发“ object:modified”或“ selection:created”之类的事件。

注意我们如何将事件直接附加到画布上（`canvas.on('mouse：down',...)`）。可以想象，这意味着事件都限于画布实例。如果页面上有多个画布，则可以将不同的事件侦听器附加到每个侦听器。它们都是独立的，并且仅尊重分配给他们的事件。

为了方便起见，Fabric进一步扩展了事件系统，并允许您将侦听器直接附加到画布对象。让我们来看看：

```js
  mounted() {
    var canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

    var rect = new fabric.Rect({
        width:100,
        height:50,
        fill:"green"
    })
    rect.on("selected",()=>{
        console.log('selected a rectangle');
    })

    var circle = new fabric.Circle({
        radius:75,
        fill:"blue",
        top:100,
        left:100
    })
    circle.on("selected",()=>{
        console.log("selected a circle");
    })
    canvas.add(rect,circle)
  },
```

![image-20210323193002477](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323193002477.png)

![image-20210323193031612](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210323193031612.png)

我们将事件侦听器直接附加到矩形和圆形实例。代替“ object:selected”，我们使用“ selected”事件。同样，我们可以使用“ modified”事件（附加到画布上时为“ object:modified”），“ rotating”事件（附加到画布上时为“ object:rotating”），依此类推。

查看此[events demo](http://fabricjs.com/events) ，以对Fabric的事件系统进行更广泛的探索。

阅读 [Part 3](http://fabricjs.com/fabric-intro-part-3)/[第三部分](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js.%20Part%203.md).

#### 常用属性

##### e.pointer/{e.e.offsetX,e.e.offsetY}

点击点在canvas中的位置



##### {e.e.clientX,e.e.clientY}/{e.e.pageX,e.e.pageY}

点击点在页面中的位置



##### {e.transform.offsetX,e.transform.offsetY}

点击点在对象中的位置
