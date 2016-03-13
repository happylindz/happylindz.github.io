---
layout:     post
title:      "Javascript - this"
date:       2016-03-04 12:00:00
author:     "Lindz"
header-img: "img/post-bg-2015.jpg"
tags:
    - Javascript
---

> 本文为原创作品。欢迎转载，转载请注明出处：[林东洲的博客 Lindz Blog](http://www.happylindz.com)。

谈及 Javascript 中的 this，竟然让人觉得头疼，它不像 Java，C++ 中的 this 指向调用 this 的该对象。

在函数中 this 到底取何值，是在函数真正被调用执行的时候确定下来的，函数定义的时候确定不了。  

因为 this 的取值是执行上下文环境的一部分，每次调用函数，都会产生一个新的执行上下文环境。

**因为 this 的取值是执行上下文环境的一部分，当你在代码中使用了 this,这个 this 的值就直接从执行的上下文中获取了，而不会从作用域链中搜寻。**

关于 this 的取值，大体上可以分为以下六种情况：

### 情况1：全局 & 调用普通函数  

在全局环境中，this永远指向window。 
 
```javascript
console.log(this === window);     //true
```

普通函数在调用时候(注意不是构造函数，前面不加 new)，其中的 this 也是指向 window。  

```javascript
var x = 10;
function foo(){
	console.log(this);     //Window
	console.log(this.x);   //10
}
foo();
```

### 情况2：构造函数

所谓的构造函数就是由一个函数 new 出来的对象，一般构造函数的函数名首字母大写，例如像 Object，Function，Array 这些都属于构造函数。

> 关于构造函数和普通函数使用上的区别，我在 [「前端 · 面试」记录一些有意思的题目(二)](http://www.happylindz.com/2016/02/23/record-front-questions/) 讲的挺全面得了，这里不再赘述。

```javascript
function Foo(){
    this.x = 10;
    console.log(this);    //Foo {x:10}
}
var foo = new Foo();
console.log(foo.x);      //10
```

上述代码，如果函数作为构造函数使用，那么其中的 this 就代表它即将 new 出来的对象。

但是如果直接调用 Foo 函数，而不是 new Foo()，那就变成情况1，这时候 Foo() 就变成普通函数。

```javascript
function Foo(){
    this.x = 10;
    console.log(this);    //Window
}
var foo = Foo();
console.log(foo.x);      //undefined
```

### 情况3：对象方法

如果函数作为对象的方法时，方法中的 this 指向该对象。

```javascript
var obj = {
    x: 10,
    foo: function () {
        console.log(this);       //Object
        console.log(this.x);      //10
    }
};
obj.foo();
```

注意：若是在对象方法中定义函数，那么情况就不同了。

```javascript
var obj = {
    x: 10,
    foo: function () {
        function f(){
            console.log(this);      //Window
            console.log(this.x);    //undefined
        }
        f();
    }
}
obj.foo();
```

函数 f 虽然是在 obj.foo 内部定义的，但它仍然属于一个普通函数，this 仍指向 window。(这是个坑，要记牢)  

在这里，如果想要调用上层作用域中的变量，这里指 obj.x，可以使用闭包。

```javascript
var obj = {
    x: 10,
    foo: function () {
        var self = this;
        function f(){
            console.log(self);      //{x: 10}
            console.log(self.x);    //undefined
        }
        f();
    }
}
obj.foo();
```


* 如果 foo 函数不作为对象方法被调用，那么结果也不一样。

```javascript
var obj = {
    x: 10,
    foo: function () {
        console.log(this);       //Window
        console.log(this.x);     //undefined
    }
};
var fn = obj.foo;
fn();
``` 

obj.foo 被赋值给一个全局变量，并没有作为 obj 的一个属性被调用，那么此时 this 的值是 window。

### 情况4：构造函数 prototype 属性

```javascript
function Foo(){
    this.x = 10;
}
Foo.prototype.getX = function () {
    console.log(this);        //Foo {x: 10, getX: function}
    console.log(this.x);      //10
}
var foo = new Foo();
foo.getX();
```

在 Foo.prototype.getX 函数中，this 指向的 foo 对象。不仅仅如此，即便是在整个原型链中，this 代表的也是当前对象的值。

### 情况5：函数用 call、apply或者 bind 调用。

```javascript
var obj = {
    x: 10
}
function foo(){
    console.log(this);     //{x: 10}
    console.log(this.x);   //10
}
foo.call(obj);
foo.apply(obj);
foo.bind(obj)();
```

当一个函数被 call、apply 或者 bind 调用时，this 的值就取传入的对象的值。

### 情况6: DOM event this

在一个 HTML DOM 事件处理程序里，this 始终指向这个处理程序所绑定的 HTML DOM 节点：

```javascript
function  Listener(){  
    document.getElementById('foo').addEventListener('click',this.handleClick);     //不是指这里的 this，这里的 this 指向 Listener 这个对象。
}
Listener.prototype.handleClick = function (event) {
    console.log(this);    //<div id="foo"></div>
}
var listener = new Listener();
document.getElementById('foo').click();
```

你也可以用通过 bind 切换上下文:

```javascript
function  Listener(){
    document.getElementById('foo').addEventListener('click',this.handleClick.bind(this));      
}
Listener.prototype.handleClick = function (event) {
    console.log(this);    //Listener {}
}
var listener = new Listener();
document.getElementById('foo').click();
```

总结一句话，就是 this 指向调用该方法的对象。

## 其它：

* this 为保留字，你不能重写 this。

```javascript
function test(){
	var this = {};     //Uncaught SyntaxError: Unexpected token this
}
```

* 宿主对象：

> 一门语言在运行的时候，需要一个环境，叫做宿主环境。  
 
> 对于JavaScript，宿主环境最常见的是web浏览器，浏览器提供了一个JavaScript运行的环境，这个环境里面，需要提供一些接口，好让JavaScript引擎能够和宿主环境对接。  
 
> JavaScript引擎才是真正执行JavaScript代码的地方，常见的引擎有V8(目前最快JavaScript引擎、Google生产)、JavaScript core。

在浏览器或者服务端( nodejs )都有自己的 JS 引擎，在浏览器中，全局对象为 window，而在 nodejs 中，全局对象为 global。 

参考链接： 
 
* [JavaScript中的this陷阱的最全收集--没有之一](https://segmentfault.com/a/1190000002640298#articleHeader1)  
* [深入理解javascript原型和闭包（10）——this](http://www.cnblogs.com/wangfupeng1988/p/3988422.html)




