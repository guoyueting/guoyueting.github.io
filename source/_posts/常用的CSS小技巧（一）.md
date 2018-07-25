---
title: 常用的CSS小技巧（一）
categories: CSS
---
实际开发过程中会遇到一些需要用CSS小技巧处理的布局问题，现在分享几个个人工作中遇到的小问题和解决方案。

## 1.inline元素间的空白间隙
这里要介绍一个神器**font-size:0**。
如果你写了个列表，因为元素节点有文本节点，在缩进代码时会占据宽度，比如：

```
<ul>
    <li>我是第一项</li>
    <li>我是第二项</li>
    <li>我是第三项</li>
    <li>我是第四项</li>
</ul>
```

设置了CSS

```
<style>
    ul {
        list-style: none;
    }
    li {
        width: 25%;
        display: inline-block;
        background: green;
        text-align: center;
        height: 40px;
        line-height: 40px;
    }
</style>
```
就会发现
![有空白间隙](http://pbzqi3myf.bkt.clouddn.com/font-size0.png)
我们为了页面代码的整洁可读性，往往会设置一些适当的缩进、换行，但当元素的display为inline或者inline-block的时候，这些缩进、换行就会产生空白，所以出现上述问题。虽然还有其他方法能解决我们因为缩进、换行而产生的问题，但此时，最合适的方法就是给li的父级ul设置**font-size: 0**， 给li设置**font-size: 16px**，如此就达到了所需效果
![空白间隙消失啦~](http://pbzqi3myf.bkt.clouddn.com/font-size1.png)

图片间的间隙问题也是因为换行、缩进。

```
<div>
     <img src="pic1.jpg">
     <img src="pic2.jpg">
</div>
```
如上，图片出现间隙后，在div设置**font-size:0**，间隙就会消失。

## 2.图片等比缩放
在做项目时遇到轮播图的图片从某服务器获取的情况，但图片大小比例却是不定的，这种情况该如何保证图片按比例显示？或者在移动端场景下，如何做图片的响应性布局？

在CSS3中，可使用`background-size`来解决针对响应性布局的背景图片自适应。

+ 浏览器支持的程度：`IE9+, Firefox4+, opera, chrome, safari5+`
+ 基本语法：`background-size: length | percentage | cover | contain`


1. `length`该属性值是设置背景图像的宽度和高度的，第一个值是宽度，第二个值是设置高度的。如果只设置第一个值，那么第二个值会自动转换为 “auto”；**调整图片到指定大小；**
2. `percentage`该属性是以父元素的百分比来设置图片的宽度和高度的，第一个值是宽度，第二个值是高度。如果只设置一个值，那么第二个值会被设置为 “auto”；**调整图片到指定大小，百分比相对于包含元素的尺寸。**
3. `cover`把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。**扩展图片来填满元素（保持像素的长宽比）；**
4. `contain`把图像扩展至最大尺寸，以使宽度和高度完全适应内容区域。 **缩小图片来适应元素的尺寸（保持像素的长宽比）**

那究竟是怎么使用的呢？举个🌰

```
// html部分
<div class="bsize1"></div>
// CSS部分
.bsize1 {
	position: relative;
	width: 28%;
	height: 0;
	padding-bottom: 17.5%;
	overflow: hidden;
	background-position: center center;
	background-repeat: no-repeat;
	background-size: cover;
	border: 1px solid #e2e5e7 !important;
}
```

## 3.文字折行
单行文本溢出

```
overflow: hidden;
text-overflow:ellipsis;
white-space: nowrap;
```
多行文本溢出

```
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
overflow: hidden;
```

`-webkit-line-clamp`用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的WebKit属性。常见结合属性：
`display: -webkit-box;`必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
`-webkit-box-orient` 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。

## 4.calc()

整体高度自适应布局怎么实现？在讲calc()之前先说一下vh和vw：

```
vw   相对于视口的宽度。视口被均分为100单位的vw
vh   相对于视口的高度。视口被均分为100单位的vh
vmax 相对于视口的宽度或高度中较大的那个。其中最大的那个被均分为100单位的vmax
vmin 相对于视口的宽度或高度中较小的那个。其中最小的那个被均分为100单位的vmin
```

+ calc 是 css3提供的一个在css文件中计算值的函数：用于动态计算长度值。
+ 需要注意的是，运算符前后都需要保留一个空格，例如：width: calc(100% - 10px)；
+ 任何长度值都可以使用calc()函数进行计算；
+ calc()函数支持 "+", "-", "*", "/" 运算；
+ calc()函数使用标准的数学运算优先级规则；

**注意！**

**如果用了CSS预处理器（LESS/SCSS）会被它们先解析，这时需要禁用解析calc(~ '100vh - 64px')**