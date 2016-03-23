---
layout:     post
title:      "「前端 · 面试」记录一些有意思的题目(一)"
date:       2016-02-19 12:00:00
author:     "Lindz"
header-img: "img/3.jpg"
tags:
    - 面试
---

> 本文记录一些在网上看到的，我认为有意思的前端面试题，欢迎转载，转载请注明出处：[林东洲的博客 Lindz Blog](http://www.happylindz.com)。

## 一、var a=["a","b","c","d"] 每过 1 秒 alert 一下数组中的值。

这题乍一看意识到使用闭包，但是难点还要每隔1秒弹出一次。
常规方法：设置闭包并且设置时间每次增长一秒。


```javascript
var arr = ['a','b','c','d','e'];
for(var i = 0,len = arr.length;i < len; i++){
	(function (j) {
		setTimeout(function () {
			alert(arr[j]);
		},1000 * (i+1))
	})(i);
}
```
ES6方法：利用ES6中let块级作用域的特性。
 
```javascript
var arr = ['a','b','c','d','e'];
for(let i = 0,len = arr.length;i < len; i++){
	setTimeout(function () {
 		alert(arr[i]);
 	},1000 * (i+1))
}
```

## 二、下面程序的执行结果是：

```javascript
var name = 'World!';
(function () {
	if (typeof name === 'undefined') {
		var name = 'Jack';
		console.log('Goodbye ' + name);
	} else {
		console.log('Hello ' + name);
	}
})();
```
这题乍一看容易认为输出"Hello World!"，其实不然，因为var name = 'Jack',存在变量提升，故这段代码在执行的时候会被解析成：

```javascript
var name = 'World!';
(function () {
	var name;
	if (typeof name === 'undefined') {
		name = 'Jack';
      	console.log('Goodbye ' + name);
	} else {
      	console.log('Hello ' + name);
	}
})();
```
故执行结果为："Goodbye Jack";若将var name = 'Jack' 的var去掉，则执行结果为Hello World!。

## 三、用CSS实现下面图片：

![triangle](/assets/2016-02-19-record-front-questions/1.png)

实现难点在于上述的三角形，可以使用css中的border来实现：  
**细节：首先div盒子宽高要设置为0；  
其次border-top/bottom/left/right来调整三角形，并且其背景色要设置为transparent(透明)而不是white。**

```css
<title>picture</title>
<style type="text/css">
#rectangle{
    background: #FFF;
    border: 10px solid #999999;
    padding:10px;
    height: 120px;
    width: 200px;
}
#circle{
    position:absolute;
    margin-left: 140px;
    margin-top: 10px;
    width: 40px;
    height: 40px;
    background: #999999; 
    -moz-border-radius: 20px; 
    -webkit-border-radius: 20px; 
    border-radius: 20px;
}
#triangle{
    width: 0; 
    height: 0; 
    position:absolute;
    margin-top: 40px;
    border-bottom: 80px solid #999999; 
    border-left: 50px solid transparent; 
    border-right: 70px solid transparent; 
}
#triangle2{
    width: 0; 
    height: 0; 
    position:absolute;
    margin-top: 60px;
    margin-left: 80px;
    border-bottom: 60px solid #999999; 
    border-left: 30px solid transparent; 
    border-right: 70px solid transparent; 
}
</style>
</head>
<body>
<div id="rectangle"><span id="circle"></span><span id="triangle"></span><span id="triangle2"></span></div>
</body>
```

## 四、实现下述题目的要求：

```javascript	
var Obj = function(msg){
	this.msg = msg;
	this.shout = function(){
		alert(this.msg);
	}
	this.waitAndShout = function(){
		//隔五秒钟后执行上面的shout方法

	}
}
```
看到此题容易以为考察setTimeout的用法，答案就写成：

```javascript
var Obj = function(msg){
	this.msg = msg;
   	this.shout = function(){
   		alert(this.msg);
	}
   	this.waitAndShout = function(){
   		//隔五秒钟后执行上面的shout方法
   			setTimeout(function () {
   				this.shout();
   			},5000);
   	}
}

var obj = new Obj('Jack');
obj.waitAndShout();
```
这样写是错误的，控制台会报错：**Uncaught TypeError: this.shout is not a function(anonymous function) @ test.html:17**  
事实上setTimeout里面的函数是个匿名函数，此时this指向window，故正确的答案:  

```javascript
var Obj = function(msg){
    this.msg = msg;
    this.shout = function(){
        alert(this.msg);
    }
    this.waitAndShout = function(){
        //隔五秒钟后执行上面的shout方法
        var self = this;
        setTimeout(function () {
            self.shout();
        },5000);
    }
}

var obj = new Obj('Jack');
obj.waitAndShout();
```

使用一个self变量来存放this，这样匿名函数就可以用self来访问Obj这个对象了，实现细节不在赘述。  
**有兴趣的同学可以看:** [JavaScript中的this陷阱的最全收集--没有之一](https://segmentfault.com/a/1190000002640298)

## 五、输出下述代码的执行结果：

```javascript
var start = new Date;
setTimeout(function(){
    console.log('fn1');
}, 20);
setTimeout(function(){
    console.log('fn2');
}, 30);
setTimeout(function(){
    console.log('another fn2');
}, 30);
setTimeout(function(){
    console.log('fn3');
}, 10);
console.log('start while');
while (new Date - start < 1000) {};
console.log('end while');
```
要做此题必须理解setTimeout是如何运行的, 简单来说，Javascript执行引擎运行时产生堆(Heap)和栈(Stack)。程序的代码一次进入栈中等待执行，在遇到WebAPIs中规定的事件如(DOM操作，ajax请求，还有setTimeout事件)时，会将这些事件添加到一个任务队列当中，因为Javascript是单线程执行，所以当事件时间到了之后，它先看看主线程中代码是否执行完，若代码还在执行，则阻塞该事件。  

> 即当主线程代码还在执行的情况下会阻塞任务队列里面的事件，即使任务队列里的事件已经达到了可执行阶段 

故前面几个setTimeout事件等待时间只有几十毫秒,但是new Date - start < 1000代码执行阻塞了这些存放在任务队列的事件，当代码执行完毕后，这些事件会按时间顺序依次执行。故正确答案为：  

```javascript
start while
end while
fn3
fn1
fn2
another fn2
```
更详细的实现细节可以查阅: [【转向Javascript系列】从setTimeout说事件循环模型 | Web前端 腾讯AlloyTeam  Blog | 愿景: 成为地球卓越的Web团队！](http://www.alloyteam.com/2015/10/turning-to-javascript-series-from-settimeout-said-the-event-loop-model/)  

明白上述题目可以试试下面这题，原理相似:

### 对于下列程序运行结果，符合预期的是(单选)：

```javascript
function f1() {
    console.time('time span');
}
function f2() {
    console.timeEnd('time span');
}
setTimeout(f1, 100);
setTimeout(f2, 200);

function waitForMs(n) {
    var now = Date.now();
    while (Date.now() - now < n) {}
}

waitForMs(500);
```

> * A、time span:700.077ms
> * B、time apan:0.066ms
> * C、time span:500.077ms
> * D、time span:100.077ms

正确答案：B
