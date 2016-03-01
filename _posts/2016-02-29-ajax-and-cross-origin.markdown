---
layout:     post
title:      "Aax - 跨域请求"
date:       2016-02-29 12:00:00
author:     "Lindz"
header-img: "img/home-bg.jpg"
tags:
   - Ajax
   - Javascript
   - PHP
---

> 欢迎转载，转载请注明出处：[www.happylindz.com](http://www.happylindz.com)。

## Ajax概念

Ajax全称为：'Asynchronous Javascript And XML'，即异步 Javascript 和 XML，指的是一种创建交互式网页开发技术。

**注意，Ajax 是一种技术，它并不是一门编程语言。**

简单说来，传统的网页如果需要更新内容，就必须要重新加载整个页面。

而通过 Ajax 在后台与服务器进行少量的数据交换，可以使网页实现异步更新。这就意味着可以在不重新加载整个网页的情况下，**对网页的某部分进行更新**。

例：
![](/assets/2016-02-29-ajax-cross-origin/1.png)

比如我在百度上搜索 ajax，其实我并没有点击‘百度一下‘按钮进行提交表单，但是页面已经向后台发起 Ajax 请求获取数据，并将获取到的数据在页面上进行局部更新。

## Ajax实战

了解了 Ajax 的概念之后，接下来就可以学习如何发起 Ajax 请求了。

在学习 Ajax 之前首先来创建一个form表单：

```html
<form>
    <label>Name:<input type='text' id="name" name="name"/></label> <br>
    <input type="submit" value="提交" id='submit'>
</form>
<div>Result:<span id="result"></span></div>
```
表单中有个输入框和提交按钮，还有一个用来存储结果的 div。  
效果：  
![](/assets/2016-02-29-ajax-cross-origin/2.png)

### Ajax对象创建

Ajax 核心是 Javascript 对象 XMLHttpRequest。在创建 Ajax 对象前需要对所使用的浏览器进行判断来创建不同的实例。

```javascript
var request;
if(window.XMLHttpRequest){
	request = new XMLHttpRequest();
}else{
	request = new ActiveXObject('Microsoft.XMLHTTP');
}
```
通过检测 window 对象中是否有 XMLHttpRequest 属性来确定浏览器是否支持标准的 XMLHttpRequest。

**注意，请不要用浏览器的 navigator.userAgent 来检测浏览器是否支持某个 Javascript 特性。因为这个字符串可以伪造，而且一般不会采用通过 IE 的版本判断 Javascript 特性。** 

当然也可以使用 try catch 语句来创建 XMLHttpRequest 对象。

```javascript
var request = null;
try{
	request = new XMLHttpRequest();
}catch(e){
	try{
		request = new ActiveXObject('Msxml2.XMLHTTP');
	}catch(e){
		request = new ActiveXObject('Microsoft.XMLHTTP');
	}
}
```
基本上常用的创建 Ajax 对象的方式就是这两种，可以选择封装在一个函数中。

> XMLHttp=new XMLHttpRequest() 已经被大多数现代标准浏览器所支持。   
> 但为了兼容IE浏览器，按微软的方式：XMLHttp=new 
> ActiveXObject("Msxml2.XMLHTTP") 支持IE6+的版本。   
> 如果捕获错误，则尝试更老的方法：XMLHttp=new 
> ActiveXObject("Microsoft.XMLHTTP") 支持IE5.5+的版本。

### 设置回调函数

在创建 Ajax 请求后，要先设置 onreadystatechange 回调函数(即在请求数据返回后进行的操作)。在回调函数中通常我们只需通过 readyState === 4 判断请求是否完成。如果已完成，再根据 status === 200 判断是否是一个成功的响应。

readyState 值：(1.2.3.4.5)

1. 请求未初始化
2. 服务器连接已建立
3. 请求已接收
4. 请求处理中
5. 请求已完成，且响应已就绪

code:

```javascript
request.onreadystatechange = function () {
    if (request.readyState === 4){      
        if (request.status === 200){      
            document.getElementById('result').innerHTML = request.responseText;       
        }
    }
}
```

### 发送请求

接下来就是要设置 XMLHttpRequest 对象中的 open() 方法，open() 方法一共接受三个参数，第一个参数指定是 GET 还是 POST，第二个参数指定 URL 地址，第三个参数指定是否使用异步发送请求，默认值是 true，所以可以不用填写。

**注意：千万不要把第三个参数设置为 false，否则浏览器将停止响应，直到 Ajax 请求完成，如果这个请求耗时10秒，那么10秒内你会发现浏览器处于'假死'状态。**

最后调用 send()方法才算真正发送请求，GET 请求不需要参数，POST 请求需要把 body 部分以字符串或者 FormData 对象传进去。

code:

```javascript
document.getElementById('submit').onclick = function () {
    var url = 'index.php?' +'name=' + document.getElementById('name'); 
    request.open('GET',url,true);
    request.send();
    return false;
}
```
给提交按钮绑定一个点击事件，在点击的时候将请求数据发送给后台，return false避免表单提交。

这样 Ajax 请求就完成了，已经可以实现不刷新页面局部刷新。

### 后台代码

这里我使用 php 来完成实例。


```php
$a[]="Anna";
$a[]="Brittany";
$a[]="Cinderella";
$a[]="Diana";
$a[]="Eva";
$a[]="Fiona";
$a[]="Gunda";
$a[]="Hege";
$a[]="Inga";
$a[]="Johanna";
$a[]="Kitty";
$a[]="Linda";
$a[]="Nina";
$a[]="Ophelia";
$a[]="Petunia";
$a[]="Amanda";
$a[]="Raquel";
$a[]="Cindy";
$a[]="Doris";
$a[]="Eve";
$a[]="Evita";
$a[]="Sunniva";
$a[]="Tove";
$a[]="Unni";
$a[]="Violet";
$a[]="Liza";
$a[]="Elizabeth";
$a[]="Ellen";
$a[]="Wenche";
$a[]="Vicky";

$name = $_GET['name'];
        
if(strlen($name) > 0){
    $result = '';
    for($i = 0; $i < count($a); $i++){
        if(strtolower($name) == strtolower($a[$i])){
            $result = "find the person: $a[$i]";
        }
    }
}
if($result == ''){
    $result = "Can't find the person: $name";
}
echo $result;
```
这样前后端就可以跑通了。

### 测试

![](/assets/2016-02-29-ajax-cross-origin/3.png)

![](/assets/2016-02-29-ajax-cross-origin/4.png)

## 安全限制

注意到了上面 URL 使用的相对路径，如果将它改成绝对路径比如：

```javascript
var url = 'http://127.0.0.1:63342/htdocs/ajax/request.php?name=' + document.getElementById('name').value;
```
将会报错，在 Chrome 的控制台里，还可以看到错误信息。

这是因为浏览器的同源策略导致的。默认情况下，JavaScript 在发送 Ajax 请求时，URL的域名必须和当前页面完全一致。

> 完全一致的意思是，域名要相同（www.example.com和example.com不同），协议要相同（http和https不同），端口号要相同（默认是:80端口，它和:8080就不同）。有的浏览器口子松一点，允许端口不同，大多数浏览器都会严格遵守这个限制。


## 跨域请求方法

一、是通过 Flash 插件发送 HTTP 请求，这种方式可以绕过浏览器的安全限制，但必须安装 Flash，并且跟 Flash 交互。不过 Flash 用起来麻烦，而且现在用得也越来越少了。

二、是通过在同源域名下架设一个代理服务器来转发，JavaScript 负责把请求发送到代理服务器：

```
'/proxy?url=http:/www.example.com'
```
代理服务器再把结果返回，这样就遵守了浏览器的同源策略。这种方式麻烦之处在于需要服务器端额外做开发。

三、JSONP
> 即JSON with Padding，是JSON的一种“使用模式”，可用于解决主流浏览器的跨域数据访问的问题。

它有个限制，只能用GET请求，并且要求返回 JavaScript。这种方式跨域实际上是利用了浏览器允许跨域引用 JavaScript 资源。

即Web页面上调用Js文件时可以不受跨域限制的影响，不仅如此，凡是拥有'src'这个属性的标签都拥有跨域的能力，比如：script, img, iframe标签。

代码如下：

```javascript
var jsonp = document.createElement('script');
jsonp.type = 'text/javascript';
jsonp.src = 'http://www.example.com/remote.js';
document.getElementsByTagName('head')[0].appendChild(jsonp);
```

四、CORS
> Cross-Origin Resource Sharing 跨域资源共享, CORS 是一种允许当前域（domain）的资源（比如html/js/web service）被其他域（domain）的脚本请求访问的机制。

注：如果浏览器支持 HTML5，那么就可以一劳永逸地使用新的跨域策略：CORS了。

#### 在了解CORS前，先来搞清楚概念：  
Origin 表示本域，也就是浏览器当前页面的域。当 JavaScript 向外域（如sina.com）发起请求后，浏览器收到响应后，首先检查 Access-Control-Allow-Origin 是否包含本域，如果是，则此次跨域请求成功，如果不是，则请求失败，JavaScript 将无法获取到响应的任何数据。

![](/assets/2016-02-29-ajax-cross-origin/5.png)

假设本域是 my.com，外域是 sina.com，只要响应头 Access-Control-Allow-Origin为 http://my.com，或者是*，本次请求就可以成功。



可见，跨域能否成功，取决于对方服务器是否愿意给你设置一个正确的 Access-Control-Allow-Origin，决定权始终在对方手中。 

服务器端对于 CORS 的支持，主要就是通过设置 Access-Control-Allow-Origin 来进行的,可以添加 header 头：

```php
//php
header("Access-Control-Allow-Origin: http://www.example.com");
```
>上面这种跨域请求，称之为“简单请求”。简单请求包括 GET、HEAD和POST(POST的Content-Type类型 仅限application/x-www-form-urlencoded、multipart/form-data和text/plain)，并且不能出现任何自定义头(例如，X-Custom: 12345),通常能满足90%的需求。

注：现代浏览器一般都是用JSONP或者CORS来完成跨域请求。

CORS与JSONP相比，更为先进、方便和可靠。

1. JSONP只能实现GET请求，而CORS支持所有类型的HTTP请求。  
2. 使用CORS，开发者可以使用普通的XMLHttpRequest发起请求和获得数据，比起JSONP有更好的错误处理。  
3. JSONP主要被老的浏览器支持，它们往往不支持CORS，而绝大多数现代浏览器都已经支持了CORS。  


