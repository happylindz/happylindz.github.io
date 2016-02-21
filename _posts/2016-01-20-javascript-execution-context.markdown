---
layout:     post
title:      "Javascript - ES3执行上下文"
date:       2016-02-20 12:00:00
author:     "Lindz"
header-img: "img/post-bg-e2e-ux.jpg"
tags:
    - Javascript
    - 前端开发
---

> 欢迎转载，转载请注明出处：[www.happylindz.com](http://www.happylindz.com)。


##  概念  
### 一、执行上下文: (Execution Context,缩写 EC) 

```javascript
	console.log('EC0');
	function funcEC1(){
		console.log('EC1');
		function funcEC2(){
			console.log('EC2');
			var funcEC3 = function(){
				console.log('EC3');
			};
		}
		funcEC2();
	}
	funcEC1();
```
在Javascript引擎解析上述代码时在执行函数会依次将其添加到栈（Stack）中，即(ECO(Global),EC1,EC2)，如下图所示：

![Stack: ExecutionContext](/assets/2016-01-20-javascript-execution-context/1.png)

执行上下文分为：全局执行上下文和函数执行上下文。

### 二、变量对象：(Variable Object VO)
是一个抽象的概念中的‘对象’，它用于存储执行上下文中的： 

1. 变量
2. 函数声明
3. 函数参数

如下述代码：  

```javascript
	var a = 10;
	function test(x){
		var b = 20;
	}
	test(30);
```
Javascript引擎会将其解析为：  

```
	VO(globalContext) = {
		a: 10,
		test: <ref to function>
	};
	VO(test functionContext){
		x:30,
		b:20
	};
```

**在浏览器中全局上下文变量对象为window，而在nodejs中全局上下文变量对象为global。**

**当js代码运行前**，在全局执行上下文中(浏览器)中就会默认添加一些变量，
如：Math,String,isNaN,window，所以在调用代码时：  

```javascript
	String(10);     		//[[global]].String(10)
	window.a = 10;  	   //[[global]].window.a = 10
	this.b = 20; 			//[[global]].b = 20
```

### 三、函数中的激活对象：(Active Object AO)

与变量对象一样，即(VO(functionContext) === AO);  
只不过AO多了一个变量，为arguments:

```javascript
	arguments = {
		callee,
		length,
		properties-indexes
	};	
```
这个可选择性忽略，没什么用。

## 函数调用中的执行上下文
代码如下：

```javascript
	function test(a, b){
		var c = 10;
		function d(){}
		var e = function _e(){};
		(function(){})();
		b = 20;
	}
	test(10);
```
### 1.变量初始化阶段：  
VO按照如下顺序填充：  (在函数执行中，AO等于VO，以下都以VO简称，不再赘述)  

1. 函数参数(若未传入，初始化该参数值为undefined)
> 这里说一下，未声明的变量和声明为undefined变量是有区别的:用alert未声明的变量会报错：ReferenceError: Can't find variable，而alert声明为undefined变量会显示undefined。
> 
> 然而如果以属性的是形式访问的话，比如window.variable，alert未声明的属性或者声明为undefined的属性都弹出undefined.

2. 函数声明 (若发生命名冲突，会覆盖之前的函数参数)
3. 变量声明 (初始化变量值为undefined，若发生命名冲突，会忽略)  

所以在执行到test(10)时，函数执行上下文变量初始化阶段为：

```javascript
	VO(test) = {
		a:10,
		b:undefined,
		c:undefined,
		d:<ref to func 'd'>,
		e:undefined
	}		
```

理解：首先添加函数参数，因为传入10，所以a:10而b：20，之后函数声明提升，d被添加VO，并指向func d，之后就是变量声明提升，c、e被依次添加到AO中。
> 注意变量d和变量e的不同，d是函数声明而e是指向函数表达式，所以在变量初始化阶段时，d已经指向函数地址，而e被赋值必须要等到执行到e = function _e(){}代码的时候

注意：函数表达式并不会影响VO；
> 注意到函数_e还有下一行代码函数(function(){})(),它们都是函数表达式，所以并不会被添加到VO变量对象里。

讲到这是不是觉得自己已经弄懂了，下面还有阐述一些细节的东西： 

* 函数声明命名冲突时覆盖函数形参:

```javascript
	function foo(x,y,z){
		function x(){};
		console.log(x);
	}
	foo(100);
```
输出结果：function (){}，因为命名冲突时函数声明命名会覆盖函数形参。

* 变量声明命名冲突时不会覆盖函数形参或者函数声明：

```javascript
	function foo(x, y, z){
		function func(){
			
		};
		var func;
		console.log(func);
	}
	func();
```
输出结果：function func(){},理由如上。

### 2.代码执行阶段： 
 
```javascript
	VO(test) = {
		a: 10,
		b: 20,
		c: 10,
		d: <ref to func 'd'>,
		e: function _e(){};
	};
``` 
 
附赠一题练习题：

```javascript	

	console.log(x);
	var x = 10;
	
	console.log(x);
	x = 20;
	
	function x(){}
	console.log(x);
	
	if(true){
		var a = 1;
	}else{
		var b = true;
	}
	
	console.log(a);
	console.log(b);
	
```
输出结果：

```
function(){}
10
20
1
undefined
```
