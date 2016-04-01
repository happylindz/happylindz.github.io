---
layout:     post
title:      "Javascript - 代码规范"
date:       2016-03-07 12:00:00
author:     "Lindz"
header-img: "img/js-bg.jpg"
tags:
    - Javascript
---

记录一些 Javascript 编写的代码规范，方便以后查询和学习。

### 在编写 Javascript 代码时应尽量避免全局变量的污染:

```javascript	
function sum(x, y){
	result = x + y;    //result 被添加到全局变量
}
```

```javascript	
function foo(){
	var a = b = 0;    //b 被添加到全局变量，被解析成 var a = (b = 0);
}
```

### 在函数顶部预先声明该函数所需要的所有局部变量

```javascript
function func(){
	var a = 1,
		b = 2,
		sum = a + b,
		obj = {};
}
```

优点：

* 提供了一个单一的地方去寻找功能所需要的所有局部变量
* 防止变量在定义之前使用的逻辑错误

如：

```javascript
function func(x){
	if(x > 1){
		var y = 1;   //因为存在变量提升(预解析)，故变量重复定义
	}else{
		var y = 2;
	}
}
```

### 在循环体中，缓存数组或者集合的长度

```javascript
for(var i = 0; len = myarray.length; i < len; i++){
		//do something
}
```

### 始终使用 === 来进行判断，避免隐式类型转换

```javascript
var zero = 0;
if(zero === false){
	//不执行，因为不存在隐式类型转换
}
```

### 避免使用 eval() 和 Function()

```javascript
eval("var local = 3; console.log(local)"); // 避免

Function("console.log('Hello World!');")(); // 避免
```

注：不应该用 eval() 来解析 json 数据，而是改用 JSON.parse()

eval 代码中任何被评估的通过 var 定义的变量会被自动变成全局变量而 Function 不会, 这样 eval 一不小心就造成了全局变量污染

```javascript
var evalString = "var x = 1; console.log(x);";
eval(evalString); // logs "1"

var funString = "var y = 2; console.log(y);";
new Function(funString)(); // logs "2"

console.log(typeof x); // number
console.log(typeof y); // "undefined"
```

另外一点，eval() 会干扰作用域链，它可以访问和修改它外部作用域中的变量，而 Function() 中只看到全局作用域。

```javascript

(function () {
    var local = 1;
    eval("local = 3; console.log(local)");  //2
    console.log(local);      //3
})();

(function () {
    var local = 1;
    Function("console.log(typeof local);")();  //undefined
})();
```

### 使用 parseInt 总是指定基数参数

```javascript
parseInt("123",8);  //八进制数转化为十进制
```

### 使用花括号时，始终与之前的语句放在同一行

错误示范：

```javascript
function func(){
    return
    {
        name:'lindz';
    };
}

console.log(typeof func());   //undefined
```

当你选择不使用分号结束一行代码时 JavaScript 会自己帮你补上，所以代码被解析为：

```javascript
function func(){
    return;
    {
        name:'lindz';      //不执行
    };
}

console.log(typeof func());   //undefined
```

### 推荐使用 // 这样的注释方式，/* */ 可能与正则表达式冲突

### 空格的使用改善代码的可读性和一致性

```javascript
* for循环分号分开后的的部分：如for (var i = 0; i < 10; i += 1) {...}
* for循环中初始化的多变量(i和max)：for (var i = 0, max = 10; i < max; i += 1) {...}
* 分隔数组项的逗号的后面：var a = [1, 2, 3];
* 对象属性逗号的后面以及分隔属性名和属性值的冒号的后面：var o = {a: 1, b: 2};
* 限定函数参数：myFunc(a, b, c)
* 函数声明的花括号的前面：function myFunc() {}
* 匿名函数表达式function的后面：var myFunc = function () {};
```




