---
layout:     post
title:      "CSS - 深入理解 line-height 属性"
date:       2016-03-16 12:00:00
author:     "Lindz"
header-img: "img/14.jpg"
tags:
    - CSS
---

> 最近看张鑫旭大神博客学习 CSS，发现这个 line-height 属性挺有意思，我理解归纳了下，写了一篇学习笔记。

## 概念

line-height 为行高，即两行文字基线之间的距离，

注：**这里行高是两行文字基线之间的距离，并不是指一行文字就没有行高了，可以这么理解：两行的定义决定了一行文字的表现。**

什么是基线呢？我们来看下面这张图：  
![](/assets/2016-03-16-css-line-height/1.png)

从图中我们可以得出，从上往下四条线分别叫：顶线，中线，基线，底线。  

**所谓的行高是指上下文本行的基线间的垂直距离，即图中两条红线间的垂直距离。**

## 属性值：

```css
elem{
	line-height: normal | <number> | <length> | <percent> | inherit
}
``` 

默认值：normal，与浏览器有关，且与元素字体相关

number：line-height: 1.5;  
根据当前元素的 font-size 大小计算

```css
elem{
	font-size: 20px;
	line-height: 1.5;  //此时行高为 20px * 1.5 = 30px
}
```

length：使用具体的长度值作为行高值。例如: line-height: 20px;

percent: 相对于设置了该 line-height 属性的元素的 font-size 大小计算

```css
elem{
	font-size:20px;
	line-height: 150%;  //行高为 30px;
}
```

inherit 继承自父元素

### Q1: 行高默认是继承的，为什么还需要 inherit？

行高虽然本身就是继承的，但诸如 input 等框元素默认行高是 normal，使用 inherit 可以让文本框样式可控性更强。

### Q2: 元素属性对行高影响?

行框高度是行内最高的行内框高度，通过 line-height 调整，内容区行高与字体尺寸有关(这个后面会讲到)，padding 不对行高造成影响。

```html
<div style="border:dashed 1px #000;margin-bottom:30px;">
    <span style="font-size:14px;background-color:#999;">This is a test</span>
</div>
<div style="border:dashed 1px #000;">
    <span style="font-size:14px;padding:20px;background-color:#999;">This is a test</span>
</div>
```

实际效果：  
![](/assets/2016-03-16-css-line-height/2.png)

可以看出：padding 对行高不起作用。(因为它们的 span 边框高度相同)

### Q3: line-height: 1.5, 150%, 1.5em 各有什么区别？

首先如果你在某元素上设置了行高和字体大小，那么其实是没差的，如：

```css
elem{
	font-size: 20px;
	line-height: 1.5 | 150% | 1.5em;   //这里行高都一样，为 30px
}
``` 

但是如果应用在元素不同则导致差异:

* line-height: 1.5 所有可继承的元素根据 font-size 重计算行高
* line-height: 150% / 1.5em 当前元素根据 font-size 计算行高，  然后继承给下面的元素

来看一段例子：

```html
<p class="p1">
    <span>我的 font-size 为60px</span>
</p>
<p class="p2">
    <span>我的 font-size 为60px</span>
</p>
```

```css
body{
    width: 400px;
}
p > span{
    font-size: 60px;
}
.p1{
    font-size: 10px;
    line-height: 1.5;
}
.p2{
    font-size: 10px;
    line-height: 150%;
}
```

效果：
 
![](/assets/2016-03-16-css-line-height/3.png)

line-height: 1.5 会继承这个值然后在与元素当前的 font-size 进行计算

而 line-height：150% 或者 1.5em 则是先于父类 font-size 计算大小，然后再继承给子元素

## 行内框盒子模型

所有内联元素的样式表现都与行内框盒子模型有关 

行内框模型一共包含着四种盒子

先来看段 html 代码：

```html
<p>这是一行普通的文字，这里有个 <em>em</em> 标签.</p>
```

在这上面普通的文字中，其实就包含着四种盒子：

* "内容区域" (content area) 是一种围绕文字看不见的盒子。"内容区域" 的大小与 font-size 大小相关;  

虽然看不见，但是我们可以简单理解为下面选中的蓝色区域：  
![](/assets/2016-03-16-css-line-height/4.png)

* "内联盒子" (inline boxes)，"内联盒子" 不会让内容成块显示，而是排成一行，如果外部包含 inline 水平的标签 (span, a, em等)，则属于 "**内联盒子**"，如果是光秃秃的文字，则属于 "匿名内联盒子";

![](/assets/2016-03-16-css-line-height/5.png)

从图中我们可以得出，有一个内联盒子 (em 标签以及内部文字) 和两个匿名内联盒子 (em 标签两边的文字)

* "行框盒子" (line boxes)，每一行就是一个 "行框盒子"，每个 "行框盒子" 有一个个 "内联盒子" (inline boxes) 组成，可以直观理解为下图：

![](/assets/2016-03-16-css-line-height/6.png)

* "包含盒子" (containing box)，此盒子由**一行行的 "行框盒子"** (line boxes) 组成：

![](/assets/2016-03-16-css-line-height/7.png)

## line-height 高度机理

**内联元素**的高度是怎么而来的呢？

我们都知道，如果没有给 **内联元素** 一段文字的话，内联元素其实是没有高度的，那么我们是不是可以说内联元素的高度其实是由文字撑开的？

其实并不是这样的，**其实是由 line-height 决定的**，我们可以做个小实验:有两个 span 元素，className 分别为 test1，test2，并添加样式：

```css
span {
	margin: 50px;
}
.test1 {
	font-size: 36px;
	line-height: 0;
	border 1px solid #ccc;
	background: #eee;
}
.test2 {
	font-size: 0;
	line-height: 36px;
	border: 1px solid #ccc;
	background: #eee;
}
```

![](/assets/2016-03-16-css-line-height/8.png)

从图中我们可以得出，测试一虽然字体很大，但是行高为 0，故盒子内容没有撑开，只剩下边框，而测试虽然字体小到让人看不见，但是由于行高为 36px，撑起了高度。

那么行高又是由什么构成的呢？

它是由内容区域和行间距所组成的，它们之间的关系为:

```code
内容区域高度 ＋ 行间距 ＝ 行高
```

之前我们介绍过内容区域，内容区域高度只与字号以及字体有关，与 line-height 没有任何关系。  

注：**在某些字体 (比如宋体) 下，其内容区域高度等于文字大小值，而有些字体下两者并不相等，总体来说只能说相近**

那么行间距又是什么？ 行间距其实就是图中 3 的那部分

![](/assets/2016-03-16-css-line-height/9.png)

**行间距说白的就是为了调节行高与内容区域高度的关系的一个变量**

我们假设字体大小等于内容区域高度，那么在字体大小和行高确定的情况下，行间距作为一个调解值，使之满足刚才说的关系：

```code
内容区域高度 ＋ 行间距 ＝ 行高
```

将行间距上下拆分，就有了 "半行间距" 的概念

如果包含盒子 (containing box) 里面有多个不同行高的内联盒子，那么我们可以简单理解为由其内部**最高的行高最高的那个盒子决定**

```html
//注意：这是在没有 height 值设定的情况下
<p style="line-height: 50px; background: #ccc">
	这是一行普通的文字, 这里有个 <em style="line-height: 80px;">em</em>
</p>
```

这里实际上 p 元素最终的行高为 80px，尽管 p 元素样式中设置为 50px，但还是 em 内联盒子的高度所撑起。

总结：  

1. 行高决定了内联盒子高度
2. 行间距可大可小，保证了高度正好等于行高
3. 在没有 height 值干扰下，元素的高度有内部最高行高的那个盒子决定

注意

## 行高与图片的关系

首先，行高并不会影响图片的大小，但是其中却也暗藏玄机。

例如：

```html
<div class="box" style="background: black;">
    <img src="1.png">
</div>
```

这里有个 div 盒子背景为黑色，里面放置一张图片，神奇的是图片下面竟多出一条黑线。

![](/assets/2016-03-16-css-line-height/10.png)

这段黑线究竟是什么呢？我们可以在 div 中再加入一些文字，例如：

```html
<div class="box" style="background: black;">
    <img src="1.png">
    <span style="background: white">我是一段文字</span>
</div>
```

结果变成:

![](/assets/2016-03-16-css-line-height/11.png)

**所以下面的黑线不是由图片引起，而是由图片后的 "隐匿文本节点" 引起**

**我们可以理解为图片后面存在着一个隐匿的文本节点 (即使图片后面没有文字)，由于文本默认是 baseline 基线对齐，故多出来的部分(黑线)就是基线到底线的距离。**

### 消除图片底部间隙的方法

* 图片块状化 - 块状元素无基线对齐

```css
img{
    display: block;
}
```

* 图片底线对齐

```css
img{
	vertical-align: bottom;
}
```

* 父元素行高足够小

```css
.box{
	line-height: 0;
}
```

## line-height 实际应用：

### 实现图片水平垂直居中:

例如： 

```html
<div class="box" style="line-height: 500px;text-align: center;background: #000;">
    <img src="1.png" style="vertical-align: middle;">
</div>
```

实际效果：

![](/assets/2016-03-16-css-line-height/12.png)

### 实现多行文本垂直居中

```html
<div style="width:150px;height:100px;line-height:100px;background-color:#ccc;font-size:0;">
    <span style="display:inline-block;font-size:20px;line-height:1.4em;vertical-align:middle;">
        This is a test.<br/>
        This is a test.
    </span>
</div>
```

效果：  

![](/assets/2016-03-16-css-line-height/13.png)

关键点: 

* 父类 font-size 大小要设为 0，不然会存在垂直偏移
* 子类 inline-block 化

### 单行文本垂直居中

```html
<div style="line-height:100px;border:dashed 1px #0e0;"> 
	This is a test.
</div>
```

效果：

![](/assets/2016-03-16-css-line-height/14.png)

参考链接：

* [CSS深入理解之line-height](http://www.imooc.com/learn/403)
* [CSS行高——line-height](http://www.cnblogs.com/dolphinX/p/3236686.html)