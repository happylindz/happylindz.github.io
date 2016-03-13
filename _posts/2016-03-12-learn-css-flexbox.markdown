---
layout:     post
title:      "CSS - Flex 布局学习笔记"
date:       2016-03-12 12:00:00
author:     "Lindz"
header-img: "img/post-bg-os-metro.jpg"
tags:
    - CSS
---

## 一、Flexbox 是什么呢？

谈到页面布局，相信很多人都依赖于 display + position + float 属性来实现，但是这些都不够语义化，而且代码写起来不够简洁优雅。

Flexbox 是 flexible box 的缩写，为“ 弹性布局 ”，**它可以更加有语义化地实现各种页面布局，给盒模型之间提供很大的灵活性**

Flexbox 一种设计来实现更复杂的页面版面布局，它本质上就是个**盒模型**的延伸，Flexbox 进一步去规范了这些盒模型之间彼此的相对关系。

Flex 布局将成为未来布局的首选方案，关于兼容性：

![](/assets/2016-03-13-learn-css-flexbox/1.jpg)

如果不考虑 IE 低版本浏览器，那就放心地使用 flexbox 来进行布局吧！

## 二、Flex 基本概念

下面简单介绍一下 flex 中一些常用术语

![](/assets/2016-03-13-learn-css-flexbox/2.jpg)

容器默认存在两条轴，水平主轴 (main axis) 和垂直的交叉轴 (cross axis) ；主轴的开始位置 (main start)，交叉轴的开始位置 (cross start)；

每个项目占据的主轴空间为 (main size), 占据的交叉轴的空间为 (cross size);

## 三、Flex 容器

首先，实现 flex 布局需要指定一个容器，任何一个容器都可以指定为 flex 布局，这样容器内的元素就可以实现 flex 布局。

```css
.container{
	display: flex | inline-flex;   //可以有两种取值
}
```

分别生成一个 block-level 或者 inline-level 的 flex 容器盒子。

webkit 内核的浏览器需要加上 -webkit- 前缀

```css
.container{
	display: -webkit-flex; 
}
```

**注意：设为 flex 布局后，子元素的 float、 clear、 vertical-align 属性将会失效**

## Flex 容器属性

有六种属性可以设在容器 container 中

* flex-direction
* flex-wrap
* flex-flow
* justify-content
* align-items
* align-content

### flex-direction: 决定主轴的方向(即项目的排列方向)

```css
.container{
	flex-direction: row | row-reverse | column | column-reverse; 
}
```

默认值: row，主轴为水平方向，起点在左端    

![](/assets/2016-03-13-learn-css-flexbox/1.png)

row-reverse：主轴为水平方向，起点在右端  

![](/assets/2016-03-13-learn-css-flexbox/2.png)

column：主轴为垂直方向，起点在上沿  

![](/assets/2016-03-13-learn-css-flexbox/3.png)

column-reverse：主轴为垂直方向，起点在下沿

![](/assets/2016-03-13-learn-css-flexbox/4.png)

### flex-wrap: 决定容器内项目是否可换行

默认情况下，项目都排在主轴线上，使用 flex-wrap 可实现项目换行

```css
.container{
	flex-wrap: nowrap | wrap | wrap-reverse;
}
```

默认值：nowrap 不换行

![](/assets/2016-03-13-learn-css-flexbox/5.png)

wrap: 项目宽度综合超出容器时换行，第一行在上方

![](/assets/2016-03-13-learn-css-flexbox/6.png)

wrap-reverse：换行，第一行在下方

![](/assets/2016-03-13-learn-css-flexbox/7.png)

###  flex-flow: flex-direction 和 flex-wrap 的简写形式

默认值为: row nowrap

```css
.container{
	flex-flow: <flex-direction> || <flex-wrap>;
}
```

### justify-content：定义了项目在主轴的对齐方式。

```css
.container{
	justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

默认值: flex-start 左对齐

![](/assets/2016-03-13-learn-css-flexbox/8.png)

flex-end：右对齐

![](/assets/2016-03-13-learn-css-flexbox/9.png)

center： 居中

![](/assets/2016-03-13-learn-css-flexbox/10.png)

space-between：两端对齐，项目之间的间隔都相等。

![](/assets/2016-03-13-learn-css-flexbox/11.png)

space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

![](/assets/2016-03-13-learn-css-flexbox/12.png)

###  align-items：定义了项目在交叉轴上的对齐方式 

```css
.container{
	align-items: flex-start | flex-end | center | baseline | strech;
}
```

默认值: stretch 如果项目未设置高度或设为 auto，将占满整个容器的高度 

![](/assets/2016-03-13-learn-css-flexbox/13.png)

flex-start：交叉轴的起点对齐

![](/assets/2016-03-13-learn-css-flexbox/14.png)

flex-end：交叉轴的终点对齐

![](/assets/2016-03-13-learn-css-flexbox/15.png)

center：交叉轴的中点对齐

![](/assets/2016-03-13-learn-css-flexbox/16.png)

baseline: 项目的第一行文字的基线对齐

![](/assets/2016-03-13-learn-css-flexbox/17.png)

### align-conten: 定义了多根轴线的对齐方式，如果项目只有一根轴线，那么该属性将不起作用

```css
.container{
	align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

## Flex 项目属性

有六种属性可运用在项目上

* order
* flex-basis
* flex-grow
* flex-shrink
* flex 
* align-self

### order：定义项目的排列顺序。数值越小，排列越靠前，默认为0。

```css
.item{
	order: <integer>;
}
```

![](/assets/2016-03-13-learn-css-flexbox/18.png)

### flex-basis: 定义了在分配多余空间之前，项目占据的主轴空间，浏览器根据这个属性，计算主轴是否有多余空间

```css
.item{
	flex-basis: <length> | auto;
}
```

默认值：auto，即项目本来的大小, 这时候 item 的宽高取决于 width 或 height 的值

**flex-basis 需要跟 flex-grow 和 flex-shrink 配合使用才能发挥效果**

### flex-grow: 定义项目的放大比例

```css
.item{
	flex-grow: <number>;
}
```

默认值: 0，即如果存在剩余空间，也不放大

![](/assets/2016-03-13-learn-css-flexbox/19.png)

如果所有项目的 flex-grow 属性都为 1，则它们将等分剩余空间。(如果有的话)  
如果一个项目的 flex-grow 属性为 2，其他项目都为 1，则前者占据的剩余空间将比其他项多一倍。

### flex-shrink: 定义了项目的缩小比例

```css
.item{
	flex-shrink: <number>;
}
```

默认值：1，即如果空间不足，该项目将缩小

**负值对该属性无效**

![](/assets/2016-03-13-learn-css-flexbox/20.png)

同理可得：

如果所有项目的 flex-shrink 属性都为 1，当空间不足时，都将等比例缩小。    
如果一个项目的 flex-shrink 属性为 0，其他项目都为 1，则空间不足时，前者不缩小。

### flex: flex-grow, flex-shrink 和 flex-basis的简写

```css
.item{
	flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

默认值：0 1 auto， 即不放大，可缩小，大小与 width、height 有关 

快捷值：auto (1 1 auto) 和 none (0 0 auto)

建议优先使用这个属性，而不是单独写三个分离的属性。

flex-grow 和 flex-shrink 可缺省，故可单单设置: flex: 2;

grow 和 shrink 是一对双胞胎，grow 表示伸张因子，shrink 表示是收缩因子。

grow 在 flex 容器下的子元素的宽度和比容器和小的时候起作用。 grow 定义了子元素的宽度增长因子，容器中除去子元素之和剩下的宽度会按照各个子元素的 grow 值进行平分加大各个子元素上。

#### 公式：

计算容器还剩空间

```code
available_space (容器还剩的空间）= container_size (容器宽度) - flex_item_total (子元素宽度之和)
```

计算增长单位

```code
grow_unit (增长单位) = available_space / flex_grow_total(子元素增长因子之和)
``` 

得到子元素的宽度

```code
flex-item-width (子元素计算得到的宽度) = flex-basis + grow-unit * flex-grow   
```

### align-self: 允许单个项目有与其他项目不一样的对齐方式

单个项目覆盖 align-items 定义的属性  
默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。

```css
.item {
	align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

<hr>

讲到这里，flex 布局的基本语法就记录完毕。

参考链接：

* [Flex 布局教程：语法篇 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool)
* [flexbox：更加优雅的Web布局](https://segmentfault.com/a/1190000002616717#articleHeader11)
* [CSS3 Flexbox](http://www.html-js.com/article/2582)
