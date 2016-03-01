---
layout: 	post
title:   "「学习 · 笔记」AJax 模拟百度搜索"
date:       2016-03-01
author:     "Lindz"
header-img: "img/post-bg-js-module.jpg"
tags:
	- Ajax
	- Javascript
	- 学习笔记
	
---

 
**在阅读此文前，建议先阅读 ![「学习 · 笔记」Ajax及跨域请求]()熟悉一下 AJax 请求，本文会跳过一些细节代码的实现说明。**


实现效果：

![](/assets/2016-03-01-ajax-baidu-search/1.png)

如上图所示，在输入搜索字的时候，百度向后台发起 AJax 请求，并将后台提示信息反馈在页面上。

实现起来并不困难，话不多说，就来亲自实践一下。

## html 部分：

首先创建 index.html，添加如下结构：

```html
<img src="bd_logo.png" id="logo">
<form>
    <input type="text" id="search" onkeyup="showHint(this.value)" />
    <input type="button" value="百度一下" id="btn"/>
</form>
```
之后在创建 basic.css，添加一些样式并引入到html中：

```css
body{
    text-align: center;
}
#logo{
    width: 300px;
}
#search{
    padding: 9px 7px;
    width: 521px;
    height: 20px;
    maxlength: 100;
    margin: 0;
    border: 1px solid #b8b8b8;
    font: 16px arial;
}
#btn{
    cursor: pointer;
    width: 102px;
    height: 38px;
    font-weight: normal;
    color: #FFF;
    background: #38f;
    line-height: 38px;
    font-size: 16px;
    font-family: Arial;
    padding: 0;
    border: 0;
    margin: 0;
}
```
这时候基本页面就有了，效果如下：  

![](/assets/2016-03-01-ajax-baidu-search/2.png)

在编写 AJax 请求时候，先在数据库创建一些伪造数据。

## 创建搜索数据：

创建一个create.sql文件，编写如下代码：

```sql
create database baidu_search;

use baidu_search;

create table myData(
id int auto_increment primary key,
data varchar(20) not null
);

insert into myData values (1,'ALin');
insert into myData values (2,'Amy');
insert into myData values (3,'Brittany');
insert into myData values (4,'Bully');
insert into myData values (5,'Eve');
insert into myData values (6,'Evita');
insert into myData values (7,'Eva');
insert into myData values (8,'Amanda');
insert into myData values (9,'Ellen');
insert into myData values (10,'Elizabeth');
insert into myData values (11,'Anna');
insert into myData values (12,'Abel');
insert into myData values (13,'Addison');
insert into myData values (14,'Alan');
insert into myData values (15,'Alfred');
insert into myData values (16,'Alger');
insert into myData values (17,'Allen');
```

接着我使用 php 来访问数据库并执行 sql 语句，创建一个 createdb.php, 编写如下代码:

```php
$servername = 'your servername';
$username = 'your username';
$password = 'your password';

$con = new mysqli($servername,$username,$password);

if(!$con){
    echo "Can't find the database.";
    exit();
}
$sql = file_get_contents('create.sql');
$con->multi_query($sql);
```
这时候数据就已经创建成功了，接着可以从终端查看添加的数据:

![](/assets/2016-03-01-ajax-baidu-search/3.png)

数据创建好后就可以开始编写 Javascript 代码了。

## Ajax 请求部分：

创建一个 search.js 文件，然后引入到 body 底部表单之后。 
 
在 js 代码中，首先创建封装好的 Ajax 对象函数，代码如下：  

```javascript
function getAjaxObject(){
    var request;
    if (window.XMLHttpRequest){
        request = new XMLHttpRequest();
    }else{
        request = new ActiveXObject('Microsoft.XMLHTTP');
    }
    return request;
}
```
之后在编写回调函数，代码如下： 

```javascript
function callback(){
    if (request.readyState === 4){
        if (request.status === 200){
            var result = document.createElement('div');
            result.id = 'result';
            document.body.appendChild(result);
            document.getElementById('result').innerHTML += request.responseText;
        }
    }
}
```
**这里动态创建一个 div，并且将后台返回的数据添加到 div 中，再将此 div 添加到 body 中。**

之后就可以创建一个 showHint 函数用来发送 AJax 请求，代码如下：

```javascript	
var request = null;
function showHint(val){
    if (document.getElementById('result')){
        var result = document.getElementById('result');
        result.parentNode.removeChild(result);
    }
    if (val.length == 0){
        return false;
    }
    request = getAjaxObject();
    if (request === null){
        alert("Your browser doesn't support AJax");
        return false;
    }
    request.onreadystatechange = callback;
    var url = 'getHint.php?q=' + val;
    request.open('GET',url,true);
    request.send();
}
```
之后将 onkeyup="showHint(this.value)" 添加到属性为 text 的 input 标签中，即：

```html
<input type="text" id="search" onkeyup="showHint(this.value)" />
```
这样每当数据一个字符的时候，将调用一次 showHint 函数用来发起 AJax 请求，达到每次输入每次查询的效果。

## 后台部分：

创建一个getHint.php文件，编写如下代码：

```php
$servername = 'your servername';
$username = 'your username';
$password = 'your password';
$db = 'baidu_search';

$con = new mysqli($servername,$username,$password);

if(!$con){
    echo "Can't find the database.";
    exit();
}
$con->select_db($db);

$sql = "Select * from myData;";
$result = $con->query($sql);

while($row = $result->fetch_array()){
    if(strtolower($q) == strtolower(substr($row['data'],0,strlen($q)))){
        echo "<p>";
        echo "<a href='#'>".$row['data']."</a><br>";
        echo "</p>";
    }
}
```

自此前后端已经可以正常的发起搜索功能。

## 美化搜索框：

在 basic.css 中添加一些样式：

```css
#result{
    text-align: left;
    width: 535px;
    position: relative;
    margin-left: 310px;
    border: 1px solid #b8b8b8;
    box-shadow: 1px 1px 1px 1px #eeeeee;
}
#result > p{
    margin: 0;
    padding: 0;
}
#result p > a{
    text-decoration: none;
    color: #000;
    line-height: 25px;
    font-size: 25px;
}
#result p:hover{
    cursor: pointer;
    background: #eee;
}
```

## 最终效果：

![](/assets/2016-03-01-ajax-baidu-search/4.png)

![](/assets/2016-03-01-ajax-baidu-search/5.png)






