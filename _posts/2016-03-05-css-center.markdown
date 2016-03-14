---
layout:     post
title:      "CSS - 居中元素"
date:       2016-03-05 12:00:00
author:     "Lindz"
header-img: "img/10.jpg"
tags:
    - CSS
---

> 本文记录一些在网上看到的，我认为有意思的前端面试题，欢迎转载，转载请注明出处：[林东洲的博客 Lindz Blog](http://www.happylindz.com)。

## 如何水平居中一个元素：

### 通用方法，在不知道具体盒子的宽高时

```css
.parent{
     position:relative;
}
.child{
     position:absolute;
     left:50%;
     transform:transformX(-50%);
}
```

情况 1：如果需要居中的元素为常规流中 display: inline 即内联元素，为父元素设置 text-align 即可。

常见的内联标签有：span a img input label br 等

```css
.parent{
	text-align:center;
}
```

情况 2：如果需要居中的元素为常规流中的 block 元素，先为元素设置宽度，再设置属性 margin 为auto。

常见的块级元素：div h1 ~ h6 table p ul li 等

```css
div{
    width: 600px;
    height: 50px;    //需要设置 div 高度，否则盒子无法撑起
    margin: 0 auto;
    background: #999;
}
```

或者：可以先将块级元素转换为 display:inline-block，然后再将其父元素 text-align:center 即可。

```css
.parent{
	text-align:center;
}
.child{
	display:inline-block;
}
```

情况 3：如果需要居中的元素为浮动元素  

1. 为元素设置高度和宽度
2. position 设为 relative
3. left 或者 right 设为 50%
4. margin-left 或者 margin-right 设为元素宽度的一半的负值

```css
div{
    height: 100px;
    width: 100px;
    background-color: #999;
    float: left;
    position: relative;
    left: 50%;
    margin-left: -50px;   
}
```

情况 4：如果需要居中的元素为绝对定位元素

1. 为元素设置宽度和宽度
2. 设置父元素 position 值为 relative
3. left 或者 right 设为 50%
4. margin-left 或者 margin-right 设为元素宽度的一半的负值

```css
.parent{
    background: #999;
    position: relative;
}
.child{
    position: absolute;
    background: #000;
    height: 500px;
    width: 800px;
    left: 50%;
    margin-left: -400px;
}
```

或者：

1. 为元素设置宽度和高度
2. 设置父元素 position 值为 relative
3. 设置 left 和 right 值为 0
4. 设置左右外边距为 auto

```css
.parent{
    background: #999;
    position: relative;
}
.child{
    position: absolute;
    background: #000;
    height: 500px;
    width: 800px;
    left: 0;
    right: 0;
    margin: 0 auto;
}
```

## 如何垂直居中一个元素：

### 通用方法：

```css
.parent{
     position:relative;
}
.child{
     position:absolute;
     top:50%;
     transform:transformY(-50%);
}
```

情况 1: 需要居中元素为单行文本，为包含文本的元素设置大于font-size的line-height

```css
.text {
    line-height: 200px;
}
```

## 绝对居中定位：

使元素在页面中水平和垂直居中：

```css
div{
    background: #000;
    width: 100px;
    height: 100px;
    margin: auto;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
}
```
