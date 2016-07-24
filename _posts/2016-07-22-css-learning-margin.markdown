---
layout:     post
title:      "CSS - 深入理解 margin"
date:       2016-07-22 12:00:00
author:     "Lindz"
header-img: "img/13.jpg"
tags:
    - CSS
---

## margin 与元素尺寸关系

1. 可视尺寸 - clientWidth (包含 border 及以内)
2. 占据尺寸 - outerWidth (包含 margin 及以内)

### margin 与可视尺寸：

1. 适用于没有设定 width/height 的普通 block 水平元素 (非 float 元素，absolute 元素)
2. 只适用于水平方向尺寸 (竖直方向的值靠内容撑开)

应用: 一侧定宽的自适应布局

```html
<img width="150" style="float:left">
<p style="margin-left:170px;">图片左...</p>
```

### margin 与占据尺寸:

1. block / inline-block 水平元素均适用
2. 与有没有设定 width / height 值无关
3. 适用于水平方向和垂直方向

应用：在滚动容器内留白

```html
<div style="height: 100px;overflow: scroll;background: #eee">
    <img src="logo.png" style="margin:50px;height: 300px;">
</div>
```

**如果在容器 div 上加上 padding: 50px 0; 无法达到上下留白的效果。**

## margin 与百分比单位:

普通元素的百分比 margin 都是相对于容器的宽度进行计算的
 
```html
<div style="width: 600px;height: 200px">
    <img style="margin:10%;">
</div>
<!--此时图片左间距和上间距均为: 60px-->
```

绝对定位元素的百分比 margin 是相对于第一个定位祖先元素  (relative/absolute/fixed) 的宽度计算的

```html
<div style="position: relative; width: 1000px;height: 400px;">
    <div style="width: 600px;height: 300px;">
        <img src="logo.png" style="margin: 10%;position: absolute">
    </div>
</div>
<!--此时图片左间距和上间距均为: 100px-->
```

## margin 重叠特性

1. block 水平元素 (不包含 float 和 absolute 元素)
2. 不考虑 writing-mode，只发生在垂直方向上 (即 margin-top 和 margin-bottom)

margin 重叠的三种情况：

1. 相邻的兄弟元素
2. 父级和第一个/最后一个子元素
3. 空的 block 元素

### 相邻的兄弟元素

```html
<p style="line-height: 2em;margin: 1em 0;background: #eee">第一行</p>
<p style="line-height: 2em;margin: 1em 0;background: #eee">第二行</p>
```

由于发生了垂直方向上的重叠，原本垂直方向上的 2em 变成了 1em

### 父子 margin 重叠

**仅限于父级和第一个/最后一个子元素**

```html
<div style="background: #eee;">
    <div style="margin-top: 80px">设置 margin-top</div>
</div>
```
子 div 上方并不会有 80px 的留白，相反，以下三种效果是等价的

```html
<div style="background: #eee;">
    <div style="margin-top: 80px">设置 margin-top</div>
</div>

<div style="background: #eee;margin-top: 80px">
    <div>设置 margin-top</div>
</div>

<div style="background: #eee;margin-top: 80px">
    <div style="margin-top: 80px">设置 margin-top</div>
</div>
```

父子 margin 重叠其他条件

margin-top 重叠:

1. 父元素非块状格式化上下文元素 (BFC)
2. 父元素没有 border-top 设置
3. 父元素没有设置 padding-top 
4. 父元素和第一个子元素之间没有 inline 元素分隔

以下几种情况均不会发生父子 margin-top 重叠现象：

```html
<div style="overflow:hidden;background: #eee;">
    <div style="margin-top: 80px">设置margin-top: 80px</div>
</div>
<br>

<div style="background: #eee; border-top: 1px solid transparent">
    <div style="margin-top: 80px">设置margin-top: 80px</div>
</div>
<br>

<div style="background: #eee;padding-top: 1px;">
    <div style="margin-top: 80px">设置margin-top: 80px</div>
</div>
<br>

<div style="background: #eee;"> &nbsp;
    <div style="margin-top: 80px">设置margin-top: 80px</div>
</div>
```

margin-bottom 与 margin-top 类似，不再赘述。

### 空的 block 元素 margin 重叠

```html
<div style="background: #eee;overflow: hidden">
    <div style="margin: 20px 0;"></div>
</div>
```

内盒子的高度仅为 20px，发生了重叠 (外部盒子仅为表现，与重叠无关)

发生空的 block 元素重叠的其它条件:

1. 空元素没有设置 border
2. 空元素没有设置 padding 
3. 空元素哪没有 inline 元素
4. 没有设置 height 或者 min-height

以下几种情况均可以避免空的 block 元素发生重叠：

```html
<div style="background: #eee;overflow: hidden">
    <div style="margin: 20px 0;border: 1px solid red"></div>
</div>
<br>

<div style="background: #eee;overflow: hidden">
    <div style="margin: 20px 0;padding: 1px;"></div>
</div>
<br>

<div style="background: #eee;overflow: hidden">
    <div style="margin: 20px 0;;">&nbsp;</div>
</div>
<br>

<div style="background: #eee;overflow: hidden">
    <div style="margin: 20px 0;height: 40px"></div>
</div>
```

### margin 重叠的计算规则

1. 正正取大值
2. 正负值相加
3. 负负最负值

正正取大值: (针对以上三种重叠进行分析)

```html
<p style="margin-bottom: 10px;">第一行</p>
<p style="margin-top: 100px">第二行</p>
<!--此时第一行与第二行的间距为 100px-->

<div style="margin-top: 20px;">
    <div style="margin-top: 100px">我是son</div>
</div>
<!--此时上间距为 100px-->

<div style="background: #eee;overflow: hidden">
    <div style="margin-top: 20px;margin-bottom: 50px;"></div>
</div>
<!--此时子 div 的占据尺寸为 50px-->
```

正负值相加：

```html
<p style="margin-bottom: -50px;">第一行</p>
<p style="margin-top: 100px">第二行</p>
<!--此时第一行与第二行的间距为 50px-->

<div style="margin-top: -50px;">
    <div style="margin-top: 100px">我是son</div>
</div>
<!--此时上间距为 50px-->

<div style="background: #eee;overflow: hidden">
    <div style="margin-top: -20px;margin-bottom: 50px;"></div>
</div>

<!--此时子 div 的占据尺寸为 30px--> 
```

### margin 重叠的存在意义:

1. 连续段落与列表中，如果没有 margin 重叠，首尾间距与其它兄弟标签 1:2  关系，排版不自然
2. web 中任何地方嵌套或者直接放入任何裸 div，都不会影响原来的布局
3. 遗落的空任意多个 <p> 元素，不要影响原来的阅读排版

### 如何避免 margin 重叠:

可以使用 ```margin-collapse``` 来设定，它有三种值:

* collapse: 默认 - 重叠
* discard: 取消 - 此时重叠部分会失效，即两元素中间无间距
* separate: 分隔 - 此时两元素中间不发生重叠，正常显示

应用: (善用 margin 重叠)

```css
//不推荐
.list{
	margin-top: 10px;
}

//推荐
.list{
	margin-top: 10px;
	margin-bottom: 10px;
}
```

**更具有健壮性，最后一个元素移除或者位置调换，均不会破坏元来的布局。**


## margin: auto 特性

元素有时候，就算没有设置 width 或者height，也会自动填充对应的方位

```html
<div style="background: #eee">我是第一行</div>
<div style="position: absolute;left: 0;right:0;background: #eee">我是第二行</div>
```

此时，如果设置 width 或 height，自动填充特性就会被覆盖掉，原本应该填充的尺寸被 width/height强制变更，而 margin：auto 就是为了填充这个变更的尺寸设计的。

```html
<div style="background: #eee;width: 400px;margin-left: auto;margin-right: 100px">我是第一行</div>
<!--距右 100px, 距左剩余空间-->
```

**如果一侧定植，一侧 auto，auto 为剩余空间大小，如果两侧均为auto，则平分剩余空间。**

例如：为什么图片设置 ```margin:0 auto``` 不水平居中?

因为此时图片是 inline 水平，就算没有 width，其他也不会占据整个容器，若设置图片为 block 水平，就算没有设置 width，也会占据整个容器 (一行)，故此时图片能够水平居中

为什么明明容器定高，元素定高，```margin: auto 0``` 无法垂直居中?

```css
.father{
	height: 200px;
	background: #eee;
}
.son{
	height: 100px;
	width: 500px;
	margin: auto;
]
```

如果 ```.son``` 没有设置 ```height:100px```，高度不会自动填充到 ```200px``` 高，所以设置高度为 100，即没有所谓的剩余空间，所以 ```margin``` 无法起到分配剩余空间的作用

### 那么，如何使用 margin 实现垂直居中

方法一: 通过 ```writing-mode``` 改变流模式，使之呈现垂直显示

```css
.father{
	height: 200px;
	width: 100%;
	writing-mode: vertical-lr;
}
.son{
	height: 100px;
	width: 500px;
	margin: auto;
}
```

方法二：使用 ```absolute``` 实现水平垂直居中

```css
.box{
	position: absolute;
	left: 0;
	right: 0;
	top: 0;
	bottom: 0;
	width: 300px;
	height: 300px;
	margin: auto;
}
```

## margin 无效情况解析

### inline 水平元素的 margin 无效

条件：非替换元素，如: ```<img>```，并且是正常的书写模式，比如:

```html
<span style="margin: 233px;">margin:233px;</span>
```

### margin 重叠

### display: table-cell

条件: ```display:table-cell/table-row```声明的 ```margin``` 无效

### 绝对定位元素

绝对定位元素非定位方位的 margin 值无效 

```html
<div style="position: absolute;top: 10%;left: 10%;margin-bottom: 100px;">我是 div </div>
<!--因为仅设置了 top / left 值,所以设置 margin 的 bottom 值是无效的.-->
```

但是实际上这部分还是有效的，只是不明显而已

```html
<div style="position: relative;height: 100px;overflow: scroll;background: #eee">
    <div style="position: absolute;width: 300px;height: 200px;background: red;top: 0;left: 0;margin-bottom: 100px"></div>
</div>
```

### 两栏 BFC 自适应布局:

```html
<div style="background:#eee">
    <img src="" style="float: left;width: 200px">
    <div style="overflow: hidden;margin-left: 250px">...</div>
</div>
```

**设置 margin-left 在 0-200px 是无效的，必须超过 200 px。**

### 内联特性导致的 margin 值无效

```html
<div style="height: 200px;background: #eee">
    <img src="logo.png" style="margin-top: -1000px;">x
</div>
```

因为 inline 水平元素默认是基线对齐，故即使 margin-top 负值到一定值后，元素也不会超出父元素。

## margin-start 和 margin-end 属性

1. 正常的流向中， ```margin-start``` 等同于 ```margin-left```，两者重叠不累加。
2. 如果水平流是从右往左的 (```direction: ltr```)，```margin-start``` 等同于 ```margin-right```
3. 在垂直流下 (```writing-mode:vertical-lr```) ```margin-start``` 等同于 ```margin-top```