# Fabric.js中文文档导航

说明：

1、本系列如果有两个超链接，则第一个是官方文档链接，第二个是中文文档链接，用`/`分隔，若不存在第二个链接或第二个链接无法跳转则代表没有或还未完成。

2、本系列大部分代码demo都是参考官方文档自己实践而成，有些地方与官方文档不一样会有说明，也有少部分没有亲自实现直接使用了官网文档的代码。

3、本系列代码使用vue语法，每篇文章的第一个代码片段会有标签和样式部分，其他代码无特殊情况只放部分mounted(){}中的代码或与上文没有重复的核心代码。

## Vue中使用fabric.js
```bash
#需要先安装canvas
npm i canvas --save
#安装fabricjs
npm i fabric --save
```
在需要使用的页面引入
```js
import { fabric } from "fabric";
```

## 官方导航

- [Home](http://fabricjs.com/)
- [Demos](http://fabricjs.com/demos)/[演示demos](https://github.com/eternitywith/fabric.js-docs-cn/tree/master/Demos)
- [Tutorial](http://fabricjs.com/articles)/[教程](https://github.com/eternitywith/fabric.js-docs-cn/tree/master/Tutorial)
- [Docs](http://fabricjs.com/docs)/[API文档](https://github.com/eternitywith/fabric.js-docs-cn/tree/master/Docs)
- [Custom Build](http://fabricjs.com/build)
- [Support](http://fabricjs.com/help)
- [Tests/Benchmarks](http://fabricjs.com/test)
- [Team](http://fabricjs.com/team)
- [Follow @fabricjs](https://twitter.com/fabricjs)

## General Support(普通帮助)

- [Fabric Gotchas](http://fabricjs.com/fabric-gotchas)/[Fabric 缺陷](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/FabricJS%20gotchas%20.md)-使用Fabric.js时最常见的问题的答案

## Upgrade Resources(升级资源)

- [Upgrade Guide](http://fabricjs.com/upgrade-guide)/[升级指南](#)-每个主要Fabric.js版本中的主要更改的摘要
- [v4.0 Breaking Changes](http://fabricjs.com/v4-breaking-changes) -/[v4.0重大更改](#)-Fabric.js v.4x中重大更改的指南
- [Changelog v2.x and up](http://fabricjs.com/changelog) /[Changelog v2.x及更高版本](#)-Fabric.js当前版本亮点
- [Fabric.js 2.0 Changes](http://fabricjs.com/changes-introduction)/[Fabric.js 2.0更改](#)-Fabric.js 2.0版中的更改的基本摘要
- [v2.0 Breaking Change Guide Part 1](http://fabricjs.com/v2-breaking-changes)/[v2.0重大更改指南第1部分](#)-第1部分，包括用于处理来自先前Fabric.js版本的JSON的示例代码
- [v2.0 Breaking Change Guide Part 2](http://fabricjs.com/v2-breaking-changes-2)/[v2.0重大更改指南第2部分](#)-v2.0的第2部分重大更改
- [Changelog v1.x](http://fabricjs.com/fabric-changelog-old) /[更改日志 v1.x](#) -Fabric.js的早期版本的发行要点（最高v1.7.23）

## Introduction to FabricJS(FabricJS简介)

帮助您开始使用Fabric.js的基本指南

- [Introduction to custom controls for Fabric 4.x](http://fabricjs.com/controls-api)/[Fabric 4.x的自定义控件简介](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20custom%20controls%20for%20Fabric%204.x.md)
- [Introduction to Fabric object cache](http://fabricjs.com/fabric-object-caching)/[Fabric对象缓存简介](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Fabric.js%20Object%20caching%20.md)
- [Introduction to Fabric.js: Part 1](http://fabricjs.com/fabric-intro-part-1)/[Fabric.js简介：第1部分](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js:%20Part%201.md)
  - Why Fabric(为什么用Fabric)
  - Objects(对象)
  - Canvas(画布)
  - Images(图像)
  - Path and PathGroup(路径和路径组)
- [Introduction to Fabric.js: Part 2](http://fabricjs.com/fabric-intro-part-2)/[Fabric.js简介：第2部分](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js.%20Part%202.md)
  - Animation(动画)
  - Image filters(图像滤镜)
  - Colors(颜色)
  - Gradients(渐变)
  - Text(文本)
  - Events(事件)
- [Introduction to Fabric.js: Part 3](http://fabricjs.com/fabric-intro-part-3) [/Fabric.js简介：第3部分](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js.%20Part%203.md)
  - Groups(组合)
  - Serialization(序列化)
  - Deserialization, SVG Parser(反序列化，SVG解析器)
  - Subclassing(子类化)
- [Introduction to Fabric.js: Part 4](http://fabricjs.com/fabric-intro-part-4) /[Fabric.js简介：第4部分](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js.%20Part%204.%20.md)
  - Free drawing(自由绘图)
  - Customization(自定义)
  - [Fabric on Node.js(Node.js中的Fabric)
- [Introduction to Fabric.js: Part 5](http://fabricjs.com/fabric-intro-part-5)/[Fabric.js简介：第5部分](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Zoom%20and%20pan,%20introduction%20to%20FabricJS%20part%205%20.md)
  - Pan and zoom the canvas(平移和缩放画布)
- [Introduction to Fabric.js: Transformations](http://fabricjs.com/using-transformations)/[Fabric.js简介：转换](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Using%20transformations,%20Introduction%20to%20Fabric.js%20Part%206%20.md)
  - Using transformations(使用转换)
- [Introduction to Fabric.js: Part 7 - Subclasses](http://fabricjs.com/subclassing-tutorial)/[Fabric.js简介：第7部分-子类](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js:%20Part%207%20-%20Subclasses.md)
  - Subclassing Textbox for use bitmap fonts(子类化文本框以使用位图字体)
- Introduction to Fabric.js: Part 8 - Clipping with clipPaths/[Fabric.js简介：第8部分-使用clipPaths进行剪辑](https://github.com/eternitywith/fabric.js-docs-cn/blob/master/Tutorial/Introduction%20to%20Fabric.js:%20Part%208%20-%20Clipping%20with%20clipPaths%20.md)
- [ClipPaths basics](http://fabricjs.com/clippath-part1)(ClipPaths基础)
  - [ClipPaths nesting and more complex scenario](http://fabricjs.com/clippath-part2)(ClipPaths嵌套和更复杂的场景)
  - [ClipPaths on canvas](http://fabricjs.com/clippath-part3)(画布上的ClipPaths)
  - [ClipPaths and absolute position](http://fabricjs.com/clippath-part4)(ClipPaths和绝对位置)

