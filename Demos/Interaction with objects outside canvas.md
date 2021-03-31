## Fabric.js demos · Interaction with objects outside canvas

```vue
<template>
  <div class="groups">
    <canvas id="canvas"></canvas>
    <button ref="btn">button</button>
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
    var canvas = this.canvas = new fabric.Canvas("canvas", {
      width: 400,
      height: 400,
      backgroundColor: "#ddd",
    });
    fabric.Object.prototype.originX = "center";
    fabric.Object.prototype.originY= "center";

    fabric.Canvas.prototype.getAbsoluteCoords = function(object){
      console.log(this);
      console.log(object);
        return {
            left:object.left+this._offset.left,
            top:object.top+this._offset.top
        };
    }

    var btn = this.$refs.btn,
    btnWidth = 85,
    btnHeight = 18;

    function positionBtn(obj){
        var absCoords = canvas.getAbsoluteCoords(obj);

        btn.style.left = (absCoords.left - btnWidth/2)+'px';
        btn.style.top = (absCoords.top - btnHeight/2)+'px';
    }

    fabric.Image.fromURL("https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/aa18972bd40735fa8d2f0c739e510fb30e240818.jpeg",function(img){
        canvas.add(img.set({left:250,top:250,angle:30}).scale(0.25));

        img.on("moving",()=>positionBtn(img));
        img.on("scaling",()=>positionBtn(img));

        positionBtn(img);
    })
  },
};
</script>
<style>
button{
  position: absolute;
}
</style>
```

![image-20210331104253930](https://gitee.com/eternitywith/blog-image-bed/raw/master/fabricjs/image-20210331104253930.png)