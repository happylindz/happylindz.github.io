---
layout:     post
title:      "css - margin 负值之美"
date:       2016-07-24 12:00:00
author:     "Lindz"
header-img: "img/14.jpg"
tags:
    - CSS	
---


## margin 负值实现两端对齐

```html
<div class="box">
    <div class="ul">
        <div class="li">列表 1</div>
        <div class="li">列表 2</div>
        <div class="li">列表 3</div>
    </div>
</div>
```

```css
.box{
    width: 1200px;
    margin: 100px auto;
    background: blue;
}
.ul{
    overflow: hidden;
}
.li{
    width: 380px;
    height: 300px;
    margin-right: 20px;
    background: green;
    float: left;
}
```

效果如下：
![](/assets/2016-07-24-css-negative-margin/1.png)

此时第三个列表右侧有 ```20px``` 的间距，我们可以通过设定负值来实现真正的两端对齐效果

```css
.box{
    width: 1200px;
    margin: 100px auto;
    background: blue;
}
.ul{
    overflow: hidden;
    margin-right: -20px;
}
.li{
    width: 386.66px;
    height: 300px;
    margin-right: 20px;
    background: green;
    float: left;
}
```

效果如下:

![](/assets/2016-07-24-css-negative-margin/2.png)

## margin-bottom 负值实现等高效果

html 代码：

```html
<div class="container">
    <div class="left"></div>
    <div class="right"></div>
    <div class="main"></div>
</div>
```

css 代码：

```css
.container{
    overflow: hidden;
    width: 960px;
    margin: 0 auto;
    border: 2px solid orange;
}
.left, .main, .right{
    padding-bottom: 2000px;
    margin-bottom: -2000px;
}
.left{
    float: left;
    width: 100px;
    background: red;
    height: 400px;
    margin-right: 30px;
}
.right{
    float: right;
    width: 200px;
    background: blue;
    margin-left: 20px;
}
.main{
    background: green;
    height: 500px;
    margin-left: 120px;
    margin-right: 220px;
}
```

盒子的高度由 .left 的 height 值撑起

## 双飞翼布局

适用于左右栏宽度固定，中间栏宽度自适应的布局。由于网页的主页部分一般在中间，很多网页都需要中间列优先加载，则这种布局刚好满足该需求。

```html
<div class="container">
    <div class="main">Main</div>
</div>
<div class="left">Left</div>
<div class="right">Right</div>
```

```css

.container{
    width: 100%;
    float: left;
}
.main{
    margin-left: 120px;
    margin-right: 220px;
    height: 400px;
    background: red;
}
.left{
    float: left;
    margin-left: -100%;
    width: 100px;
    height: 300px;
    background: blue;
}

.right{
    float: left;
    margin-left: -200px;
    width: 200px;
    height: 500px;
    background: yellow;
}
```

实现效果:

![](/assets/2016-07-24-css-negative-margin/3.png)

## 负边距实现水平垂直居中效果

适用于宽高已知的盒模型

```css
.box{
	width: 200px;
	height: 100px;
	background: #eee;
	position: absolute;
	left: 50%;
	top: 50%;
	margin-left: -100px;
	margin-top: -50px;
}
```

## 去除列表最后一个 li 元素的 border-bottom

在实际应用中，我们经常给列表添加 border-bottom 的值，最后一个 li 的 border-bottom 往往会与外边框的重合，视觉上不太雅观，往往需要溢移除。

```html
<ul>
    <li>第一个</li>
    <li>第二个</li>
    <li>第三个</li>
    <li>第四个</li>
    <li>第五个</li>
    <li>第六个</li>
</ul>
```

```css
ul{
    padding: 0;
    list-style: none;
    margin: 20px;
    width: 390px;
    background: #eee;
    border: 2px solid #D7E2EC;
    overflow: hidden;
}
ul li{
    height: 25px;
    line-height: 25px;
    padding: 5px;
    border-bottom: 1px dotted #D5D5D5;
    margin-bottom: -1px;
}
```

**理解：这里的 ```margin-bottom: -1px;``` 并不会改变 ```li:border-bottom``` 消失，只是说原本每个 li 占据的高度变小了 1px，使得整体变得更加紧凑，然后 ul 的边框正好覆盖掉了最后一个 li 的 ```border-bottom``` 的值**

