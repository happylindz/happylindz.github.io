---
layout:     post
title:      "「前端 · 面试」记录一些有意思的题目(二)"
date:       2016-02-23 12:00:00
author:     "Lindz"
header-img: "img/post-bg-rwd.jpg"
tags:
    - 面试
    - 前端开发
---

> 本文记录一些在网上看到的，我认为有意思的前端面试题，欢迎转载，转载请注明出处：[林东洲的博客 | Lindz Blog](http://www.happylindz.com)。


## 输出下面程序题的结果：

```javascript
function Foo() {
    getName = function () { alert (1); };
    return this;
}
Foo.getName = function () { alert (2);};
Foo.prototype.getName = function () { alert (3);};
var getName = function () { alert (4);};
function getName() { alert (5);}

//请写出以下输出结果：
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```

先来理解下题目，首先创建了一个Foo()函数，然后在给Foo函数增添一个函数属性getName,在Foo原型上增添一个getName函数属性，然后再以函数表达式和函数声明分别创建了一个同名函数getName。  

#### 第一问：Foo.getName()返回2。  

#### 第二问：首先需要知道函数表达式和函数声明的区别。

> 函数声明具有变量声明提升(即所有声明变量或声明函数都会被提升到当前函数的顶部)

例：

```javascript	
console.log('x' in window);
console.log(typeof func);
var x = 0;
function func(){}
```
js引擎会将其解析为：

```javascript
var x;
function func(){}
console.log('x' in window);    //true
console.log(typeof func);      //function
x = 0;
```
如果能明白的话就能理解下面代码的输出： 

```javascript
console.log(x);     //function(){}
var x = 1;          
console.log(x);     //1
function x(){}
console.log(x);     //1
```
所以js引擎在解析代码时候就变成了：

```javascript
//省略部分代码
function Foo() {
    getName = function () { alert (1); };
    return this;
}
var getName;
function getName() { alert (5);}
getName = function () { alert (4);};

getName();       //4
```
后面函数表达式的赋值将前面的getName值个覆盖了，所以第二问输出4。

#### 第三问：首先执行Foo()函数，并执行getName的赋值语句。
> 它会查找当前作用域上是否存在这个变量，如果没有就向它上层作用域继续寻找，直到找到window全局作用域，若还是没有则在全局作用域上创建getName变量，  

因为window域中存在着getName这个变量即(var getName = function () { alert (4);};)，所以将其替换为(getName = function () { alert (1); };)
并返回window对象，所以Foo().getName()的结果为1；

若代码改为：

```javascript
function Foo() {
    var getName = function () { alert (1); };
    return this;
}
Foo().getName();     //TypeError: Foo().getName is not a function. 
```
创建getName时候多加一个var，即在当前作用域中添加一个局部变量getName，而在执行完毕后该局部变量就会被回收，并且该函数返回window全局对象，故在访问window中的getName会报错，因为没有创建window对象无此变量。

#### 第四问：正如上一问，因为全局作用于window中getName被替换。  
即(getName = function () { alert (1); };)
故此时getName();输出结果为1。

#### 第五问 new Foo.getName(); ,此处考察的是js的运算符优先级问题。

根据图中优先级的描述：  
![优先级](/assets/2016-02-23-record-front-questions/1.png)  
可以得知：可以得知点（.）的优先级高于new操作，所以原式相当于是：

```javascript
new (Foo.getName)();
```
所以结果为2；

#### 第六问 new Foo().getName()

根据表的执行结果为：(new Foo()).getName();
### 所以接下来先讨论一下js构造函数返回值的问题：

js返回值分为三类：  
1、没有返回值则创建一个空对象，并把函数中与this绑定的属性添加；

```javascript
function F(){ this.name = 'foo';}
var obj = new F();   // obj: {name:'foo'} 创建一个对象，并绑定函数中this所绑定的属性
```
2、若有返回值则检查返回值的类型，若返回值为非引用类型，比如基本类型:(string,number,boolean,null,undefined)，则js引擎会忽略该返回值，并且创建一个新对象，并把函数中与this绑定的属性添加；

```javascript
function F(){ this.name = 'foo'; return true;}
var obj = new F();   // obj: {name:'foo'} 创建一个对象，并绑定函数中this所绑定的属性
```
3、若返回值是引用类型，则实际返回值为这个引用类型。

```javascript
function F(){this.name = 'foo'; return {x:1};}
var obj = new F();    //obj: {x:1}
```
如果理解这些，再来看下面一个例子：

```javascript
var obj;
function F1(){this.name = 'foo'; return 'a';}
obj = new F1();    //obj: F1{name: 'foo'}

function F2(){this.name = 'foo'; return String('a');}
obj = new F2();    //obj: F2{name: 'foo'}

function F3(){this.name = 'foo'; return new String('a');}
obj = new F3();    //obj: String {0: "a", length: 1}

```
从例子中我们可以得出：无论 ‘a’还是String('a')创建的都是基本类型值，而new String('a')则创建一个字符串包装对象。

原题中，返回的是this，而this在构造函数中本来就代表当前实例化对象。  
之后调用实例化对象的getName函数，因为在Foo构造函数中没有为实例化对象添加任何属性，遂到当前对象的原型对象（prototype）中寻找getName，故最终输出3。

#### 第七问：new new Foo().getName(); 同样是运算符优先级问题。
执行结果为：new ((new Foo()).getName)();  
先初始化Foo的实例化对象，然后将其原型上的getName函数作为构造函数再次new，故最终结果为3
