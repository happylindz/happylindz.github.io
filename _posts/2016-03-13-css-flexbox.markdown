---
layout:     post
title:      "CSS - Flexbox 实例"
date:       2016-03-13 12:00:00
author:     "Lindz"
header-img: "img/post-bg-js-version.jpg"
tags:
    - CSS
---

介绍一些 Flexbox 在页面布局上的应用

注：这里不再阐述原理及实现细节，若想学习 Flexbox 如何使用，可以查看 [CSS - Flex 布局学习笔记](http://www.happylindz.com/2016/03/12/learn-css-flexbox/)

## 一、实现三列自适应布局

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
    display: flex;
}
.left{
    width: 100px;
    background: orange;
    order: -1;      //使 .left 排列在 .main左边
}
.right{
    width: 200px;
    background: lavender;
}
.main{
    background: mediumaquamarine;
    flex: 1;
    height: 300px;         //假设内容有 300px 高度
    margin-left: 20px;
    margin-right: 10px;
}
```

效果：

![](/assets/2016-03-13-css-flexbox/1.png)

不仅实现了三列布局，中间内容自适应，并且 .main 的内容优先加载，并且设置布局十分简单。

## 二、实现水平垂直居中效果

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

效果：

![](/assets/2016-03-13-css-flexbox/2.png)

## 三、实现等高列布局

html 代码：

```html
<div class="container">
    <div class="item"></div>
    <div class="item main"></div>
    <div class="item"></div>
</div>
```

css 代码：

```css
.container{
    display:flex;
    align-items: stretch;
    width: 960px;
    margin: 0 auto;
}
.item{
    flex: 1;
    background: orange;
    margin: 10px;
}
.main{
    height: 400px;       //假设内容高度撑起由 400px
}
```

这里我们假设左右栏的高度由中间内容自适应，而假设中间内容高度由 400px;

效果：

![](/assets/2016-03-13-css-flexbox/3.png)

## 四、实现流式布局

html 代码：

```html
<div class="container">
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
</div>
```

css 代码：

```css
.container{
    display: flex;
    width: 600px;
    margin: 0 auto;
    flex-wrap: wrap;
}
.item{
    margin: 20px;
    flex: none;
    width: 100px;
    height: 100px;
    background: lightblue;
    line-height: 100px;
    color: #fff;
    font-size: 40px;
    text-align: center;
}
```

效果：

![](/assets/2016-03-13-css-flexbox/4.png)

## 五、悬挂式布局

有时，主栏的左侧或右侧，需要添加一个图片栏。

效果：

![](/assets/2016-03-13-css-flexbox/5.png)


## 六、固定的底栏

有时，页面内容太少，无法占满一屏的高度，底栏就会抬高到页面的中间。这时可以采用Flex布局，让底栏总是出现在页面的底部。

效果：

![](/assets/2016-03-13-css-flexbox/6.png)

参考链接：

* [Flex 布局教程：实例篇 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)
