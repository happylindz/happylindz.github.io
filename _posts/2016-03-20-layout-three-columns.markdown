---
layout:     post
title:      "CSS - 浅谈页面布局"
date:       2016-03-20 12:00:00
author:     "Lindz"
header-img: "img/16.jpg"
tags:
    - CSS
---

## 三列布局：左右列定宽，中间自适应

### 方法一：使用流体布局实现 (基本常见)

html 代码：

```html
<div class="left"></div>
<div class="right"></div>
<div class="main"></div>
```

css 代码：

```css
.left{
    float: left;
    height: 200px;
    width: 100px;
    background: red;
}
.right{
    width: 200px;
    height: 200px;
    background: blue;
    float: right;
}
.main{
    margin-left: 120px;
    margin-right: 220px;
    height: 200px;
    background: green;
}
```

兼容性良好  兼容 IE 6+  
缺点：主要内容模块无法最先加载，当页面中内容较多时会影响用户体验。因此为了解决这个问题，有了如下的布局方案双飞翼布局


### 方法二：使用 BFC 原理实现

BFC 的规则之一，就是 BFC 区域，不会与 float box 重叠，因此我们可以利用这一点来实现3列布局。

html 代码：

```html
<div class="left"></div>
<div class="right"></div>
<div class="center"></div>
```

css 代码：

```css
.left{
    width: 100px;
    height: 200px;
    background: red;
    margin-right: 20px;
    float: left;
}
.right{
    width: 200px;
    height: 200px;
    background: blue;
    margin-left: 10px;
    float: right;
}
.center{
    overflow: hidden;
    height: 200px;
    background: green;  
}
```

兼容性： IE6+ 都没有问题  
缺点：主要内容模块无法最先加载，当页面中内容较多时会影响用户体验。因此为了解决这个问题，有了如下的布局方案双飞翼布局

> 在IE6，IE7中使用 overflow:hidden 会没有效果，子元素超出父元素部分任然不会隐藏，解决办法就是在该元素的父级添加如下属性即可 position: relative。


### 方法三：双飞翼布局

html 代码：

```html
<div class="content">
    <div class="main"></div>
</div>
<div class="left"></div>
<div class="right"></div>
```

css 代码：

```css
.content{
    float: left;
    width: 100%;
}
.main{
    height: 100px;
    background: green;
    margin-left: 220px;
    margin-right: 110px;
}
.main::after {       //用于清除浮动
  	display: block;
  	content: '';
  	font-size: 0;
  	height: 0;
  	clear: both;
  	zoom: 1;
}
.left{
    float: left;
    width: 200px;
    height: 100px;
    background: red;
    margin-left: -100%;
}
.right{
    float: left;
    width: 100px;
    height: 100px;
    background: orange;
    margin-left: -100px;
}
```

优点：兼容性: IE6+ 并且主题内容优先加载

缺点：html代码结构稍微复杂

### 方法四：圣杯布局

html 代码：

```html
<div class="container">
    <div class="main"></div>
    <div class="left"></div>
    <div class="right"></div>
</div>
```

css 代码：

```css
.container{
    margin-left: 120px;
    margin-right: 220px;
}
.main{
    height: 300px;
    background: red;
    float: left;
    width: 100%;
}
.left{
    float: left;
    margin-left: -100%;
    position: relative;
    left: -120px;
    height: 300px;
    background: blue;
    width: 100px;
}
.right{
    float: left;
    width: 200px;
    margin-left: -200px;
    right: -220px;
    background: green;
    height: 300px;
    position: relative;
}
```

只是和双飞翼布局上有一些细节的区别。总体来说差别不大。但 html 结构相对简单，也是可以先加载主体内容


### 方法五：flex 布局

html 代码：

```html
<div class="container">
    <div class="left"></div>
    <div class="main"></div>
    <div class="right"></div>
</div>
```

css 代码：

```css
.container{
    display: flex;
}
.left{
    width: 100px;
    height: 300px;
    background: red;
}
.right{
    width: 200px;
    height: 300px;
    background: blue;
}
.main{
    background: green;
    flex: 1;
    height: 300px;
    margin-left: 20px;
    margin-right: 10px;
}
```

### 方法六：table 布局

html 代码：

```html
<div class="container">
    <div class="left"></div>
    <div class="main"></div>
    <div class="right"></div>
</div>
```

css 代码：

```css
.container{
    display: table;
    width: 100%;
}
.left, .main, .right{
    display: table-cell;
}
.left{
    width: 200px;
    height: 300px;
    background: red;
}
.main{
    background: green;
}
.right{
    width: 100px;
    height: 300px;
    background: blue;
}
```

### 方法七：绝对定位布局  

html 代码：

```html
<div class="container">
    <div class="main"></div>
    <div class="left"></div>
    <div class="right"></div>
</div>
```

css 代码：

```css
.container{
    position: relative;
}
.left{
    position: absolute;
    width: 100px;
    height: 300px;
    background: red;
    left: 0;
    top:0;
}
.right{
    position: absolute;
    width: 100px;
    height: 300px;
    background: blue;
    right:0;
    top: 0;
}
.main{
    height: 400px;
    background: green;
    margin: 0 120px;
}
```



