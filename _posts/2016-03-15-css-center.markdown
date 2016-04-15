---
layout:     post
title:      "CSS - 居中元素"
date:       2016-03-15 12:00:00
author:     "Lindz"
header-img: "img/10.jpg"
tags:
    - CSS
---

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

## 水平垂直居中的办法

### 方法一：绝对居中定位

条件: 块元素尺寸已知

css 代码：

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

优点：

1. 不仅可以实现在正中间，还可以在正左方，正右方
2. 支持百分比%属性值和min-/max-属性
3. 支持跨浏览器,包括IE8-IE10
4. 只用这一个类可实现任何内容块居中


### 方法二：负边距居中

条件: 块元素尺寸已知

css 代码：

```css
div{
    width: 100px;
    height: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-left: -50px;
    margin-top: -50px;
    background: red;
}
```

优点：良好的跨浏览器特性,兼容IE6-IE7

缺点：不能自适应。不支持百分比尺寸和min-/max-属性设置

### 方法三：变形（Transforms）

元素高度可变

css 代码：

```css
div{
    width: 50%;
    height: 50%;
    position: absolute;
    top: 50%;
    left: 50%;
    -webkit-transform: translate(-50%,-50%);
    -ms-transform: translate(-50%,-50%);
    transform: translate(-50%, -50%);
    background: red;
}
```

优点：内容可变高度

缺点：

1. IE 8 不支持
2. 属性需要写浏览器厂商前缀
3. 可能干扰其他transform效果

### 方法四：Flexbox

css 代码：

```css 
html, body{
    height: 100%;
}
body{
    display: flex;
    justify-content: center;
    align-items: center;
}
div{
    height: 100px;
    width: 100px;
    background: red;
}
```

这是CSS布局未来的趋势。  
Flexbox是CSS3新增属性,设计初衷是为了解决像垂直居中这样的常见布局问题。  
优点：内容块的宽高任意,优雅的溢出。  
缺点：

1. IE8/IE9不支持。
2. 运行于现代浏览器上的代码需要浏览器厂商前缀。
3. 需要特定的父容器。

### 方法五：使文本居中的方法，使用 line-height 属性

css 代码：

```css
.parent{
	width:100px;
	height:100px;
	line-height:100px;
	text-align:center;
	background:gray;
}
```

### 方法六：使用 ::before 元素

css 代码：

```css
.parent {
    position: fixed;
    display: block;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    text-align: center;
    background: rgba(0, 0, 0, .5);
}
.parent :before {
    content: '';
    display: inline-block;
    vertical-align: middle;
    height: 100%;
}

.parent .child {
    display: inline-block;
    vertical-align: middle;
    width: 60px;
    height: 60px;
    line-height: 60px;
    color: red;
    background: yellow;
}
```


