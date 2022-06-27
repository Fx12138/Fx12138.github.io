---
title: css揭秘
date: 2021-08-02 18:04:46
tags: 前端
---

# 形状

## 自适应椭圆

### 椭圆

到 border-radius，有一个鲜为人知的真相：它可以单独指定水平 和垂直半径，只要用一个斜杠（/）分隔这两个值即可。这个特性允许我 们在拐角处创建椭圆圆角.因此，如果我们有一个尺寸为 200px×150px 的元素，就可以把它圆角的两个半径值分别指定为元素宽高的 一半，从而得到一个精确的椭圆：

```css
border-radius: 100px / 75px;
```

但是，这段代码存在一个很大的缺陷：只要元素的尺寸发生变化， border-radius 的值就得跟着改。

其实，border-radius 这个属性还有另外 一个鲜为人知的真相，它不仅可以接受长度值，还可以接受百分比值。

```css
border-radius: 50% / 50%
```

## 平行四边形

### 嵌套元素方案

使用transform: skewX(-45deg);可以使图形倾斜,但是这会使里面的内容也倾斜.这时可以给里面的内容加一个div使其反方向倾斜就好了

```html
<a href="#yolo" class="button">
 <div>Click me</div>
</a>
<style>
.button { transform: skewX(-45deg); }
.button > div { transform: skewX(45deg); }
</style>
```

### 伪元素方案

我们希望伪元素保持良好的灵活性，可以自动继承其宿主元素的尺寸， 甚至当宿主元素的尺寸是由其内容来决定时仍然如此。一个简单的办法是 给宿主元素应用 position: relative 样式，并为伪元素设置 position:  absolute，然后再把所有偏移量设置为零，以便让它在水平和垂直方向上都 被拉伸至宿主元素的尺寸。代码看起来是这样的.

用伪元素生成的方块是重叠在内容之上的，一旦给它设置背景， 就会遮住内容（参见图 3-16）。为了修复这个问题，我们可以给伪元素设置z-index: -1 样式，这样它的堆叠层次就会被推到宿主元素之后。

```css
.button{
 position: relative;
 width: 100px;
 height: 100px;
 line-height: 100px;
 text-align: center;
}
.button::before{
 content: '';
 position: absolute;
 top: 0;
 right: 0;
 bottom: 0;
 left: 0;
 z-index: -1;
 background-color: #AAAAAA;
 transform: skew(45deg);
}
```

## 菱形

### 基于变形的方案

**这种方法只适用于图片为正方形!!!!!!**

主要的思路与前一篇攻略“平行四边形”中讨论的第一个解决方案一 致：需要把图片用一个 

 包裹起来，然后对其应用相反的 rotate() 变形样式:

| 值              | 描述                             |
| --------------- | -------------------------------- |
| rotate(*angle*) | 定义 2D 旋转，在参数中规定角度。 |

```html
<div class="shapeBox">
	<img src="bg.jpg" />
</div>

<style>
.shapeBox{
  width: 100px;
  height: 100px;
  overflow: hidden;
  background-color: #00FFFF;
  transform: rotate(45deg);
}
.shapeBox img{
  max-width: 100%;
  transform: rotate(-45deg);
}
</style>
```

但是,这并不能达到我们所期望的效果,如下图,这会使内图形变为一个八角形,因为max-width: 100%这个属性.100% 会被解析为容器 （.picture）的边长。但是，我们想让图片的宽度与容器的对角线相等，而 不是与边长相等。因此，把 max-width 的值设置为142%

![image-20210806134403770](image-20210806134403770.png)

如果用 scale() 变形样式来把这个图片放大，实际上会更加合理

```css
.picture {
 width: 400px;
 transform: rotate(45deg);
 overflow: hidden;
}
.picture > img {
 max-width: 100%;
 transform: rotate(-45deg) scale(1.42);
}
```

### 裁切路径方案

 clip-path使用方式创建元素的可显示区域。区域内的部分显示，区域外的隐藏。

```css
img {
 clip-path: polygon(50% 0, 100% 50%,
 50% 100%, 0 50%);
 transition: 1s clip-path;
}
img:hover {
 clip-path: polygon(0 0, 100% 0,
 100% 100%, 0 100%);
}
```

