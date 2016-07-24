---
layout:     post
title:      "CSS - 深入理解 relative"
date:       2016-07-24 12:00:00
author:     "Lindz"
header-img: "img/2.jpg"
tags:
    - CSS
---

## 对 absolute 起限制作用

1. 限制 left / top / right / bottom 定位
2. 限制 z-index 层级
3. 限制在 overflow

### 限制 left / top / right / bottom 定位

absolute 绝对定位是相对于非 static 值的父元素  

### 限制 z-index 层级:

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
  
此时 div2 层级比 div1 高，优先显示. 然而:  

```css  
//sass
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
此时尽管 child1 的 z-index 比 child2 小，但是 child1 依然在 child2 上面.

**注：若 relative 元素 z-index 设置为 auto，则不会限制其内部 absolute 元素的层叠关系**

### 限制在 overflow 下:  

```css
.parent{
	overflow:hidden;
	.child{
		position:absolute;
	}
}
``` 

若子元素超出父元素部分，overflow 并不会对其进行限制；

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

## 限制 fixed： 
  
relative只能限制z-index，对上述其他属性不起作用。  

## relative 定位特性

1. 相对自身
2. 无侵入

### 相对自身：

与 absolute (相对于非 static 值的父元素定位)不同，relative 相对于其原本位置进行定位。

### 无侵入: 	 

margin-top 与 top 进行对比

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

**此时在 div1 后面的元素会随着 div1 的变动也随之改变位置，而 div2 后面的元素并不会改变位置。即 margin 定位会影响其它元素的布局，而 top / left 定位不会。**  

在相对定位里，如果 top / bottom 和 left / right 对立属性同时存在时的表现：  

* top 起作用而 bottom 不起作用  
* left 起作用而 right 不起作用  

## relative 与层级关系

### 提高层叠上下文：  

在正常的 dom 流下，一般是后面的元素覆盖掉前面的元素，假如我们希望前面的元素显示在上面，可以使用 ```position:relative``` 来提高其层次。

```css
.box1{
    width: 400px;
    height: 300px;
    background: red;
    float: left;
    position: relative;
}
.box2{
    float: left;
    width: 400px;
    height: 300px;
    background: blue;
    margin-left: -100px;
}
```

在未设置 ````position:relative``` 的情况下，```box2``` 将会覆盖 ```box1```，而设置之后，```box1```  将会显示在 ```box2``` 上方

## relative 的最小化影响原则

**即尽量降低 relative 属性对其他元素或布局的潜在影响。**

### 尽量避免使用 relative：

即避免使用 relative 属性的父元素来限制使用 absolute 属性的子元素。  

### relative最小化：

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

这样可避免因为relative带来的子元素div层级关系的问题。


 



