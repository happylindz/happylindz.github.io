---
layout:     post
title:      "Nodejs - SuperAgent 模块学习"
date:       2016-04-14 12:00:00
author:     "Lindz"
header-img: "img/2.jpg"
tags:
    - Nodejs
---

## SuperAgent 介绍

superagent 是一个 nodejs 不错的第三方模块，主要是用来充当 http 客户端，专注于处理服务器 / 客户端的 http 请求。

我们都知道 nodejs 里面自带的 http 模块也有处理 http 请求的功能，不过 superagent 提供了更加简单、优雅的 API，让你在处理请求的时候更加方便，用起来也十分简单。

## SuperAgent 安装

学习 nodejs 的话应该对 npm 有一定的了解，打开命令行在制定路径下直接输入如下即可安装:

```node
npm install superagent
```

## SuperAgent 用法

先来对比一下 superagent 和 http 模块在请求百度首页并打印出 html：

```javascript
//superagent
var superagent = require('supeagent');
superagent.get('http://www.baidu.com')
			.end(function(req, res){
				console.log(res.text);
			});

//http 
var http = require('http');
http.get('http://www.baidu.com', function(res){
    var html = "";
    res.on('data', function(chunk){
        html += chunk.toString();
    });
    res.on('end', function(){
        console.log(html);
    });
})
```

http 模块是基于流机制的，当有数据流过来时就会触发 data 事件，然后在流关闭时候会触发 end 事件。

### 发起 HTTP 请求：

我们常见的 HTTP 请求有 GET、POST、DELETE、HEAD 等，在发送请求时一般有下列两种方式：

```javascript
//这里以 GET 举例，其他类似
//方法 1:
superagent.get('http://www.baidu.com').end(function(req, res){
	//do something
});

//方法 2:
superagent('GET', 'http://www.baidu.com').end(function(req, res){
	//do something
});
```

**这里有一点需要注意，就是只要当调用了 end() 方法之后，这个请求才会发出。在调用 end() 之前的所有动作其实都是对请求的配置。**

### 设置请求头：

> 在写爬虫的时候设置请求头十分有用，因为有些网站设置了一些限制条件，比如它会匹配你的请求头里面的信息，像 User-agent、Referer 等，如果匹配不成功的话将拒绝给你返回数据。

在 superagent 里通过 set() 方法设置请求头，一般有以下两种方式：

```javascript
方法 1:单独设置
superagent.get('http://www.baidu.com')
			.set('Referer', 'http://www.example.com')
			.set('Accept', 'text/json');
			
方法 2:一起设置
superagent.get('http://www.baidu.com')
			.set({
				'Referer': 'http://www.example.com',
				'Accept': 'text/json'
			});			
```

### 设置请求主体：

#### GET 请求参数：

> 有时候 GET 请求中需要带上参数，比如 URL 像：http://www.example.com?name=lindz&age=21

我们可以通过 query() 方法来使 URL 后面带上参数

```javascript
方法 1:
superagent.get('/')
			.query({name:'lindz'})
			.query({age:21})

方法 2:
superagent.get('/')
			.query('name=lindz')
			.query('age=21');
	
方法 3:
superagent.get('/')
			.query({name:'lindz', age: 21});
			
方法 4:
superagent.get('/')
			.query('name=lindz&age=21');
```

#### POST 请求主体：

我们可以通过 send() 方法来发送 POST 请求主体

```javascript
//比如传递 json 数据
superagent.post('/')
      		.send('{"name":"lindz","age":21');
      		
//或者
superagent.post('/')
			.send({name:"lindz"})
      		.send({age:21})
```

### HTTP 响应属性：

* res.text 包含被解析的响应数据 (如：html)
* res.body 包含解析后的数据，目前只支持三种格式: (application/x-www-form-urlencoded、application/json 和 multipart/form-data)
* res.header 响应头，是一个对象
* res.type & res.charset 类型和编码格式
* res.status状态码

### 中断请求 & 暂停请求：

分别对应 req.abort() 和 req.timeout(ms);

### 错误处理:

```javascript
superagent.end(function(err, res){
	if(err){
		throw err;
	}
});
//或者
superagent.on('error', function(err){
	console.log(err);
});
```




