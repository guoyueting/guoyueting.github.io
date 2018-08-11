---
title: 常用的CSS小技巧（二）
categories: CSS
---

## tranform知多少
transform是变换，包括旋转rotate、扭曲skew、缩放scale和移动translate，可以改变对象的外形和位置。transform的转换，并不是动态的，而是最终时刻的状态。

> transform: rotate | scale | skew | translate | matrix

CSS3中的坐标系如图所示。后续介绍会用上。
![XYZ坐标轴](http://pbzqi3myf.bkt.clouddn.com/xyz.png)

### （1）rotate
可进行2D或者3D旋转。其中常用的rotate(angle)定义2D旋转，angle定义旋转的角度，正数表示顺时针旋转，负数表示逆时针旋转。

* 定义

|值|描述|
|---|---|
|rotate(angle)|定义 2D 旋转，在参数中规定角度（正数顺时针、负数逆时针）|
|rotate3d(x,y,z,angle)|定义 3D 旋转|
|rotateX(angle)|定义沿着 X 轴的 3D 旋转|
|rotateY(angle)|定义沿着 Y 轴的 3D 旋转|
|rotateZ(angle)|定义沿着 Z 轴的 3D 旋转|

* 使用方法

```
div
{
    transform: rotate(30deg);
    -ms-transform: rotate(30deg); /* IE 9 */
    -webkit-transform: rotate(30deg); /* Safari and Chrome */
}
```

* 效果

![rotate](http://pbzqi3myf.bkt.clouddn.com/rotate.png)


### (2) scale
scale(x,y)方法让元素可以在x,y轴方向上进行拉伸或缩放。伸缩程度由x，y参数决定。scale的参数如果只有一个，则默认为等比例变化。（例如scale(1.5)代表放大1.5倍）。

* 定义

|值|描述|
|---|---|
|scale(x[,y]?)|定义 2D 缩放转换|
|scale3d(x,y,z)|定义 3D 缩放转换|
|scaleX(x)|通过设置 X 轴的值来定义缩放转换|
|scaleY(y)|通过设置 Y 轴的值来定义缩放转换|
|scaleZ(z)|通过设置 Z 轴的值来定义 3D 缩放转换|

* 使用方法

```
div
{
    transform: scale(2,3); /* 标准语法 */
    -ms-transform:scale(2,3); /* IE 9 */
    -webkit-transform: scale(2,3); /* Safari */
}
```

* 效果

![scale](http://pbzqi3myf.bkt.clouddn.com/scale.png)


### (3) translate
translate(x,y)方法，让元素相对当前位置在x、y方向上偏移给定参数的距离。它是translateX()和translateY()的结合。

* 定义

|值|描述|
|---|---|
|translate(x,y)|定义 2D 转换|
|translate3d(x,y,z)|定义 3D 转换|
|translateX(x)|定义转换，只是用 X 轴的值|
|translateY(y)|定义转换，只是用 Y 轴的值|
|translateZ(z)|定义 3D 转换，只是用 Z 轴的值|

* 使用方法

```
div
{
    transform: translate(50px,100px);
    -ms-transform: translate(50px,100px); /* IE 9 */
    -webkit-transform: translate(50px,100px); /* Safari and Chrome */
}
```

* 效果

![scale](http://pbzqi3myf.bkt.clouddn.com/translate.png)

### Q：tranlateZ(0)为什么可以提高浏览器渲染的性能?

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在这里要特别提出`tranlateZ(0)`,可以利用GPU加速，提高浏览器渲染的性能。为什么`translateZ(0)`可以提升渲染的性能呢？？这得从浏览器的渲染原理说起。

<big>** 浏览器渲染原理 **</big>
在从服务器拿到数据后，浏览器会先解析三类东西：

* 解析html,xhtml,svg这三类文档，形成dom树。
* 解析css，产生css rule tree。
* 解析js，js会通过api来操作dom tree和css rule tree。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;解析完成之后，浏览器引擎会通过dom tree和css rule tree来构建rendering tree：

* rendering tree和dom tree并不完全相同，例如：<head></head>或display:none的东西就不会放在渲染树中
* css rule tree主要是完成匹配，并把css rule附加给rendering tree的每个element

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在渲染树构建完成后，

* 浏览器会对这些元素进行定位和布局，这一步也叫做reflow(回流)或者layout
* 浏览器绘制这些元素的样式，颜色，背景，大小及边框等，这一步也叫做repaint(重绘)
* 然后浏览器会将各层的信息发送给GPU，GPU会将各层合成；显示在屏幕上

即渲染树构建完成后，浏览器要做的步骤是：
reflow --->  repaint ---> composite

reflow和repaint都是耗费浏览器性能的操作,为了仅发生composite，我们做动画的css property必须满足以下三个条件：

1. 不影响文档流。
2. 不依赖文档流。
3. 不会造成重绘。

满足以上以上条件的css property只有transform和opacity。

这样做有两个优势:

1. 动画将会非常流畅
2. 动画不在绑定到CPU，即使js执行大量的工作；动画依然流畅。

GPU有2个问题：

1. 一个或多个没有自己复合层的元素要出现在有复合层元素的上方，它就会拥有自己的复合层；这种情况被称为隐式合成。
2. 使用GPU动画需要发送多张渲染层的图像给GPU，GPU也需要缓存它们以便于后续动画的使用。

所以——

* 保持动画的对象的z-index尽可能的高。理想的，这些元素应该是body元素的直接子元素。当然，这不是总可能的。所以你可以克隆一个元素，把它放在body元素下仅仅是为了做动画。

* 将元素上设置will-change CSS属性，元素上有了这个属性，浏览器会提升这个元素成为一个复合层（不是总是）。这样动画就可以平滑的开始和结束。但是不要滥用这个属性，否则会大大增加内存消耗。




<big>** tranlateZ(0)为什么可以提高浏览器渲染的性能？**</big>
这个问题是因为使用transform和opacity做CSS动画的时候，会将元素提升为一个复合层；而使用js操作css属性做动画时，必须使用translateZ或will-change才能将元素强行提升至一个复合层。

元素本身使用transform和opacity做CSS动画的时候，会提前告诉GPU动画如何开始和结束及所需要的指令；所以会创建一个复合层（渲染层），并把页面所有的复合层发送给GPU；作为图像缓存，然后动画的发生仅仅是复合层间相对移动。当元素使用css动画时，在控制台中的layers可以看到，会有两个layer。

而使用js做动画，js必须在动画的每一帧计算元素的状态；发送给GPU，但不会将元素提升至一个复合层；所以想让元素提升至一个复合层，必须使用translateZ或will-change: transform, opacity。当我们使用js动画时，在控制台，可以看到只有一个layer，添加translateZ(0)的时候，可以看到又有两个layer了。


> 简而言之，是因为transform不会触发repaint，而是会创建composited layer，
GPU会来执行transform的操作，将浏览器的渲染过程交给GPU处理，而不是使用自带的比较慢的渲染器。
这样就可以使得动画更加顺畅。







