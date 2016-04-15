---
layout:     post
title:      "「学习 · 笔记」CSS之relative"
date:       2016-01-28 12:00:00
author:     "Lindz"
header-img: "img/2.jpg"
tags:
    - CSS
---

## relative与absolute：
  
* 限制left/top/right/bottom定位:

> absolute绝对定位是相对于非static值的父元素  

* 限制z-index层级:

code：  

```css   
.div1{    
	position:absolute;    
	z-index:1;
}  

.div2{
	position:absolute;
	z-index:2;
}
```   
此时div2层级比div1高，优先显示.  
然而:  

```css  
//less
.parent1{     
	position:relative;
	z-index:2;
	.child1{
		postion:absolute;
		z-index:1;
	}
}

.parent2{     
	position:relative;
	z-index:1;
	.child2{
		postion:absolute;
		z-index:2;
	}
}
```
此时尽管child1的z-index比child2小，但是child1依然在child2上面.

* 限制在overflow下:  

code:  

```css
.parent{
	overflow:hidden;
	.child{
		position:absolute;
	}
}
``` 
若子元素超出父元素部分，overflow并不会对其进行限制；

```css
.parent{
	overflow:hidden;
	position:relative;
	.child{
		position:absolute;
	}
}
```
此时若子元素超出父元素部分，因为overflow子元素超出部分将被隐藏； 

## relative与fixed： 
  
> relative只能限制z-index，对上述其他属性不起作用。  

## relative与定位:

### 特性：

* 相对自身：

> 与absolute(相对于非static值的父元素定位)不同，relative相对于其原本位置进行定位。   

* 无侵入: 	 

margin-top与top进行对比   
code：	

```css
div1{
	position: relative;
	margin-top: -100px;
}

div2{
	position: relative;
	top: -100px;
}

```

> 此时在div1后面的元素会随着div1的变动也随之改变位置，而div2后面的元素并不会改变位置。 

注：若top/bottom和left/right对立属性同时存在时的表现：  
top起作用而bottom不起作用；  
left起作用而right不起作用。  

## relative与层级关系：

### 提高层叠上下文：  

> 在层叠底部的元素添加relative属性值可将该元素置顶。

## relative的最小化影响原则：

即尽量降低relative属性对其他元素或布局的潜在影响。 

#### 1. 尽量避免使用relative：

> 即避免使用relative属性的父元素来限制使用absolute属性的子元素。  

#### 2. relative最小化：
code:

```html
<div style="postion:relative;">
	<div style="position:absolute;top:0;right:0;"></div>
	<div></div>
	<div></div>
	<div></div>
</div>
```
可改为： 

```html
<div style="postion:relative;">
	<div style="position:absolute;top:0;right:0;"></div>
</div>
<div>
	<div></div>
	<div></div>
	<div></div>
</div>

```
> 这样可避免因为relative带来的子元素div层级关系的问题。


 



