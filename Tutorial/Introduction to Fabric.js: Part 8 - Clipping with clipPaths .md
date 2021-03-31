# Introduction to Fabric.js/ Part 8 - Clipping with clipPaths 

## ClipPaths basics

### The new clipPath property(新的clipPath属性)

在2.4.0中，我们为所有对象引入了clipPath属性。 ClipPath将替换`clipTo:funcion(){}`，以实现相同的灵活性，但具有更好的兼容性。 

ClipPath需要对象缓存。

### How to use it(如何使用)

将您自己的clipPath创建为普通的Fabric对象，并将其分配给您要裁剪的对象的clipPath属性。 根据SVG规范中的定义，clipPath没有stroke，并用黑色填充，与黑色像素重叠的对象的像素将是可见的，而其他像素则不可见。 

让我们从一些基本示例开始，然后看看它的外观。 在此第一个示例中，红色的rect被一个圆圈夹住，只有圆形内的部分可见。虽然不是很有用，但是基本功能是这样的。 

请注意：clipPath从对象的中心开始定位，对象originX和originY不起任何作用，而clipPath originX和originY起作用。定位逻辑与fabric.Group相同。

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
      height: 400,
      backgroundColor: "#ddd",
    });

    var clipPath = new fabric.Circle({
        radius:40,
        left:-40,
        top:-40
    })

    var rect = new fabric.Rect({
        width:200,
        height:100,
        fill:"red"
    })

    rect.clipPath = clipPath;
    this.canvas.add(rect)
  },
};
</script>
<style>
</style>
```

![image-20210330095035682](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330095035682.png)

我们可以剪辑一个组：

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

    var clipPath = new fabric.Circle({
        radius:100,
        top:-100,
        left:-100
    })

    var group = new fabric.Group([
        new fabric.Rect({width :100,height:100,fill:"red"}),
        new fabric.Rect({width :100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width :100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width :100,height:100,fill:"green",left:100,top:100}),
    ]);

    group.clipPath = clipPath;
    this.canvas.add(group)
  },
```

![image-20210330095642004](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330095642004.png)

或者我们可以使用组进行剪辑。如果是组，请记住组中的每个对象在逻辑上或与其他对象成对，否则就不会有非零或奇数的裁剪规则。

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

    var clipPath = new fabric.Group([
        new fabric.Circle({radius:70,top:-70,left:-70}),
        new fabric.Circle({radius:40,top:-95,left:-95}),
        new fabric.Circle({radius:40,top:15,left:15}),
    ],{left:-95,top:-95})

    var group = new fabric.Group([
        new fabric.Rect({width:100,height:100,fill:"red"}),
        new fabric.Rect({width:100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width:100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width:100,height:100,fill:"green",left:100,top:100}),
    ])

    group.clipPath = clipPath;
    this.canvas.add(group);
  },
```

![image-20210330100942531](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330100942531.png)

[Continue with more examples](http://fabricjs.com/clippath-part2)



## More advanced use cases(更多高级用法)

### Nesting clipPaths(折叠clipPaths)

clipTo和canvas.clip（）用法的一个问题是您一次最多只能有一个clipPath。 

通过此实现，clippaths可以具有自己的剪切路径。尽管手动编程不太直观，但它允许将clipPaths与gheter相交。

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

    var clipPath = new fabric.Circle({radius:70,top:-50,left:-50});
    var innerClipPath = new fabric.Circle({radius:70,top:-90,left:-90});
    clipPath.clipPath = innerClipPath;

    var group = new fabric.Group([
        new fabric.Rect({width:100,height:100,fill:"red"}),
        new fabric.Rect({width:100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width:100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width:100,height:100,fill:"green",left:100,top:100}),
    ])

    group.clipPath = clipPath;
    this.canvas.add(group)
  },
```

![image-20210330101926870](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330101926870.png)

组内对象中的ClipPath应与组本身的clipPath隔离：

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

  	var clipPath = new fabric.Circle({radius:100,top:-100,left:-100});

    var small = new fabric.Circle({radius:50,top:-50,left:-50})

    var group = new fabric.Group([
        new fabric.Rect({width:100,height:100,fill:"red",clipPath:small}),
        new fabric.Rect({width:100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width:100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width:100,height:100,fill:"green",left:100,top:100}),
    ])

    group.clipPath = clipPath;
    this.canvas.add(group)
  },
```

![image-20210330102300786](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330102300786.png)

### Clipping with Text

同样，使用clipTo也无法进行文本剪切，因此开发人员通常不得不依靠这种模式。

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

    var clipPath = new fabric.Text(
        'Hi I\'m the \nnew ClipPath!\nI hope we\'ll\nbe friends',
        {top:-100,left:-100}
    );

    var group = new fabric.Group([
        new fabric.Rect({width:100,height:100,fill:"red"}),
        new fabric.Rect({width:100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width:100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width:100,height:100,fill:"green",left:100,top:100}),
    ])

    group.clipPath = clipPath;
    this.canvas.add(group)
  },
```

![image-20210330102719377](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330102719377.png)

[Continue with more examples](http://fabricjs.com/clippath-part3)



## Clipping the canvas(剪裁画布)

我们可以像对对象一样将clipPath应用于StaticCanvas。在这种情况下，clipPath受缩放和平移的影响，与Object.clipPath相反，它位于左上角。

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

		var group = new fabric.Group([
        new fabric.Rect({width:100,height:100,fill:"red"}),
        new fabric.Rect({width:100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width:100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width:100,height:100,fill:"green",left:100,top:100}),
    ])

    var clipPath = new fabric.Circle({radius:100,top:0,left:50})

    this.canvas.clipPath = clipPath;
    this.canvas.add(group)
  },
```



![image-20210330103259220](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330103259220.png)

与旧的clipTo函数一样，除非您将canvas.controlsAboveOverlay设置为true，否则clipPath也是剪辑控件。

```vue
mounted() {
    this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

		this.canvas.controlsAboveOverlay = true;

		var group = new fabric.Group([
        new fabric.Rect({width:100,height:100,fill:"red"}),
        new fabric.Rect({width:100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width:100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width:100,height:100,fill:"green",left:100,top:100}),
    ])

    var clipPath = new fabric.Circle({radius:100,top:0,left:50})

    this.canvas.clipPath = clipPath;
    this.canvas.add(group)
  },
```



![image-20210330103731185](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330103731185.png)

### Animating the clipPaths

ClipPath可以像其他任何对象一样设置动画。画布clipPath动画非常有效，而为Object动画时，每次都会使对象缓存无效。

```vue
mounted() {
    var canvas = this.canvas = new fabric.Canvas("canvas", {
      width: 300,
      height: 300,
      backgroundColor: "#ddd",
    });

		var clipPath = new fabric.Rect({width:100,height:100,top:0,left:0})

    this.canvas.controlsAboveOverlay = true;

    function animateLeft(){
        clipPath.animate({
            left:200,
        },{
            duration:900,
            onChange: canvas.requestRenderAll.bind(canvas),
            onComplete:animateRight,
        })
    }
    function animateRight(){
        clipPath.animate({
            left:0,
        },{
            duration:1200,
            onChange:canvas.requestRenderAll.bind(canvas),
            onComplete:animateLeft,
        })
    }

    function animateDown(){
        clipPath.animate({
            top:100,
        },{
            duration:500,
            onChange:canvas.requestRenderAll.bind(canvas),
            onComplete:animateUp,
        })
    }

    function animateUp(){
        clipPath.animate({
            top:0,
        },{
            duration:400,
            onChange:canvas.requestRenderAll.bind(canvas),
            onComplete:animateDown
        })
    }

    var group = new fabric.Group([
        new fabric.Rect({width:100,height:100,fill:"red"}),
        new fabric.Rect({width:100,height:100,fill:"yellow",left:100}),
        new fabric.Rect({width:100,height:100,fill:"blue",top:100}),
        new fabric.Rect({width:100,height:100,fill:"green",left:100,top:100}),
    ],{
        scale:1.5
    })

    animateLeft();
    animateDown();

    this.canvas.clipPath = clipPath;
    this.canvas.add(group)
  },
```



![Mar-30-2021 10-57-38](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/Mar-30-2021%2010-57-38.gif)

[Continue with more examples](http://fabricjs.com/clippath-part4)



## Clipping the objects with absolute clipPaths(使用绝对定位的clipPaths剪切对象)

我们可以对一个对象应用clipPath，指定它应该是绝对定位的。这意味着clipPath不是相对于对象的中心，而是仅仅定位在画布上。
物体将不会移动，而clipPath将保持稳定。效果是clipPath在画布上创建了一个形状，并且只允许它自己的对象出现在画布中。

```vue
mounted() {
   var canvas =  this.canvas = new fabric.Canvas("canvas", {
      width: 500,
      height: 500,
      backgroundColor: "#ddd",
    });

    var clipPath = new fabric.Rect({width:500,height:250,top:0,left:0,absolutePositioned:true})

    var clipPath2 = new fabric.Rect({width:500,height:250,top:250,left:0,absolutePositioned:true})

    fabric.Image.fromURL("http://fabricjs.com/assets/dragon.jpg",function(img){
        img.clipPath = clipPath;
        img.scaleToWidth(500);
        canvas.add(img);
    })

    fabric.Image.fromURL("http://fabricjs.com/assets/dragon2.jpg",function(img){
        img.clipPath =clipPath2;
        img.scaleToWidth(500);
        img.top = 250;
        canvas.add(img)
    })
  },
```

![image-20210330111408043](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330111408043.png)

## Inverted clipPaths(颠倒的clipPaths)

我们还可以裁剪具有从clipPath定义的倒置形状的对象。 警告：如果创建一个组，则必须反转该组。反转组中用作clipPath的单个对象不会产生任何效果，因此不受支持。 

您也可以使用单个对象的globalCompositeOperation获得很酷的效果，但是也不支持。

```vue
mounted() {
   var canvas =  this.canvas = new fabric.Canvas("canvas", {
      width: 500,
      height: 500,
      backgroundColor: "#ddd",
    });

    var clipPath = new fabric.Circle({radius:100,top:-200,left:-200});

    var clipPath2 = new fabric.Circle({radius:100,top:0,left:0})

    var clipPath3 = new fabric.Circle({radius:100,top:0,left:-200})

    var clipPath4 = new fabric.Circle({radius:100,top:-200,left:0})

    var g = new fabric.Group([clipPath,clipPath2,clipPath3,clipPath4])

    g.inverted = true;

    fabric.Image.fromURL("http://fabricjs.com/assets/dragon.jpg",function(img){
        img.clipPath = g;
        img.scaleToWidth(500);
        canvas.add(img);
    })
  },
```



![image-20210330112036636](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330112036636.png)

不颠倒的效果( `g.inverted = false`)如下：

![image-20210330112322753](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210330112322753.png)