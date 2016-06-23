---
layout:     post
title:      "gulp 入门笔记"
date:       2016-06-21 12:00:00
author:     "Lindz"
header-img: "img/11.jpg"
tags:
    - 数据结构
---

## (一) 简介

gulp 是前端开发过程中对代码进行构建的工具，是自动化项目的构建利器；她不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成；使用她，我们不仅可以很愉快的编写代码，而且大大提高我们的工作效率。

这里简单地跟 webpack 进行对比：

> Gulp / Grunt 是一种工具，能够优化前端工作流程。比如自动刷新页面、combo、压缩 css、js、编译 less 等等。简单来说，就是使用 Gulp/Grunt，然后配置你需要的插件，就可以把以前需要手工做的事情让它帮你做了。

>  browserify / webpack / seajs / requirejs 这四个都是JS模块化的方案，seajs / require : 是一种在线"编译" 模块的方案，相当于在页面上加载一个 CMD/AMD 解释器，而browserify / webpack : 是一个预编译模块的方案不需要在浏览器中加载解释器。


## (二) 安装

gulp 是基于 nodejs 的，所以在使用之前必须先安装 nodejs。

全局安装 gulp:

```code
npm install gulp -g 
```

在项目里安装 gulp:

```code
npm install gulp --save-dev
```

## (三) Gulp API

gulp 一共只有四个方法，分别是

* gulp.task()
* gulp.src()
* gulp.dest()
* gulp.watch()

### 1. gulp.task(name[, deps], fn)

定义一个 gulp 任务，deps 可选参数，包含对依赖的任务

### 2. gulp.src(globs[, options])

src 方法是指定需要处理的源文件的路径，gulp 借鉴了 Unix 操作系统的管道（pipe）思想，前一级的输出，直接变成后一级的输入，gulp.src 返回当前文件流至可用插件；

### 3. gulp.dest(path[, options])

dest方法是指定处理完后文件输出的路径

#### 4. gulp.watch(glob [, opts], tasks) 

watch方法是用于监听文件变化，文件一修改就会执行指定的任务

## (四) 模块使用

在项目根目录里创建一个文件 gulpfile.js，以后我们就需要在这文件添加代码来使用 gulp 为我们自动构建项目了。

### gulp 任务

先来看一段代码：

```javascript	
var gulp = require('gulp');       //引入 gulp

gulp.task('default', function () {
    console.log('Hello World');
});
```

gulp 的默认事件是 default，当你没有执行某个事件时候，gulp 会自动执行 default 这项任务，之后我们可以执行这个gulpfile.js

![](/assets/2016-06-21-gulp-learninigt/1.gif)

如果你想执行指定名字的任务

```javascript	
var gulp = require('gulp');      

gulp.task('task1', function () {
    console.log('Hello task 1');
});
```

执行结果：

![](/assets/2016-06-21-gulp-learninigt/2.gif)

在一个任务中包含另一个任务

```javascript
var gulp = require('gulp');

gulp.task('task1', function () {
    console.log('Hello task 1');
});

gulp.task('task2', function () {
    console.log('Hello task 2');
});

gulp.task('default', ['task1', 'task2'], function(){
    console.log("Hello World");
});
```

执行结果：

![](/assets/2016-06-21-gulp-learninigt/3.gif)

### 压缩 javascript 代码

首先需要安装 gulp-uglify

```code
npm install gulp-uglify --save-dev
```

将根目录 js 文件下的所有 js 文件全部压缩并将压缩好的文件放到 minjs 文件下

```javascript
var gulp = require('gulp');
var uglify = require("gulp-uglify");

gulp.task('minjs', function(){

    gulp.src('js/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('minjs'));

});
```

### 监视文件修改

可以用 watch 来监视某些文件的变化，当文件修改了之后会执行相应的代码。这里我们以上面的压缩 Javascript 代码举例，当修改了 js 文件夹下的 js 文件代码之后压缩文件也会同步进行修改。

```javascript
var gulp = require('gulp');
var uglify = require("gulp-uglify");

gulp.task('minjs', function(){

    gulp.src('js/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('minjs'));

});

gulp.task('watch', function(){

    gulp.watch('js/*.js', ['scripts']);

});

gulp.task('default', ['watch']);
```

### 将 Sass 编译为 Css

 首先需要安装模块 gulp-ruby-sass
 
 ```javascript
 npm install gulp-ruby-sass --save-dev
 ```
 
 实例代码：
 
```javascript
var gulp = require('gulp');
var sass = require('gulp-ruby-sass')
gulp.task('sass', function () {
   sass("sass/**/*.scss").pipe(gulp.dest('css'));
});
```


### 监视代码错误

**当你在敲代码的时候容易漏掉一些符号或者一些小错误，这时候如果gulp 有监视任务的时候，它就会抛出异常并停止监视。如果每次因为一点小错误就导致需要重新开启监视模式，那未免显得麻烦，比如：**

![](/assets/2016-06-21-gulp-learninigt/4.gif)


首先需要安装模块 gulp-plumber

```javascript
npm install gulp-plumber --save-dev
```

代码如下:

```javascript
var plumber = require('gulp-plumber');

gulp.task('scripts', function () {

    gulp.src('js/*.js')
        .pipe(plumber())
        .pipe(uglify())
        .pipe(gulp.dest('minjs'));

});
```

实现效果如下: 

![](/assets/2016-06-21-gulp-learninigt/5.gif)

还有另一种实现方式不需要额外加载模块，代码如下:

```javascript
gulp.task('scripts', function () {

    gulp.src('js/*.js')
        .pipe(uglify())
        .on('error', console.error.bind(console))
        .pipe(gulp.dest('minjs'));

});
```

如果发生错误即 error，该错误会被捕获并打印出来，同样不会停止监视

### 修改文件自动刷新页面

比如你在修改页面的样式的时候，经常需要看一下效果，所以需要一个工具使得每次修改代码，页面总能自动刷新，并不用你手动的点击 F5。

首先，你需要在 Chrome 商店里安装 livereload 插件，并且使用npm 安装 gulp-livereload。

```javascript
npm install gulp-livereload
```

比如你想要修改了 sass 目录下的所有 scss 文件，那么当你打开的页面加载了引用了 scss 文件生成的 css 文件，那么该页面就会自动刷新，代码如下：

```javascript
var livereload = require('gulp-livereload');
gulp.task('watch', function () {

    var server = livereload.listen();
    gulp.watch('sass/*.scss', ['sass']);

});
gulp.task('sass', function () {
    sass("sass/**/*.scss", {
        style: 'nested'
    })
        .on('error', console.error.bind(console))
        .pipe(gulp.dest('css')).pipe(livereload());
});
```

### 图片压缩

首先你需要安装模块 gulp-imagemin

```
npm install imagemin --save-dev
```

代码如下:

```javascript
var gulp = require('gulp');
var imagemin = require("gulp-imagemin");
gulp.task('image', function () {
   gulp.src('img/*')
       .pipe(imagemin())
       .pipe(gulp.dest("build/img"));
});
```

### 自动处理浏览器前缀

在开发的时候需要经常考虑浏览器的兼容性问题，比如我们在使用 transition 的时候，需要:

```css
a{
    display : -webkit-box;
    display : -ms-flexbox;
    display : flex
}
```

这里就是要解决当你编写样式的时候不用考虑各个版本浏览器的前缀， gulp 会自动帮你添加，具体代码如下:

```javascript
var prefixer = require('gulp-autoprefixer');
gulp.task('autoprefixer', function () {
    gulp.src('css/*.css')
        .pipe(prefixer({
            browsers: ['last 2 versions']  //即兼容所有浏览器最新的两个版本，大于两版本的就不添加了
        }))
        .pipe(gulp.dest('build/css'));
});
```

使用前后对比:

```css
//index.css
//使用前:
div {
    transform: rotateX(360deg);
    transition: all 2s ease;
}

a {
    display : flex
}

//使用后：
div {
    -webkit-transform: rotateX(360deg);
            transform: rotateX(360deg);
    -webkit-transition: all 2s ease;
    transition: all 2s ease;
}

a {
    display : -webkit-box;
    display : -ms-flexbox;
    display : flex
}
```

除了 last 2 version 的配置，还可以有其他的配置。

* last 2 versions: 主流浏览器的最新两个版本
* last 1 Chrome versions: 谷歌浏览器的最新版本
* last 2 Explorer versions: IE的最新两个版本
* last 3 Safari versions: 苹果浏览器最新三个版本
* Firefox >= 20: 火狐浏览器的版本大于或等于20
* iOS 7: IOS7 版本
* Firefox ESR: 最新ESR版本的火狐
* > 5%: 全球统计有超过5%的使用率

### 合并 javascript 文件

首先你需要安装模块 gulp-concat

示意代码：

```javascript
var gulp = require('gulp'),
    concat = require('gulp-concat');
 
gulp.task('testConcat', function () {
    gulp.src('src/js/*.js')
        .pipe(concat('all.js'))    //合并后的文件名
        .pipe(gulp.dest('build/js'));
});
```

### 压缩 html 页面

使用 gulp-htmlmin 压缩 html，可以压缩页面 javascript、css，去除页面空格、注释，删除多余属性等操作。

```
npm install --save-dev gulp-htmlmin
```

示意代码:

```javascript
var gulp = require('gulp');
var htmlmin = require("gulp-htmlmin");

gulp.task('htmlmin', function () {
    var options = {
        removeComments: true,//清除HTML注释
        collapseWhitespace: true,//压缩HTML
        collapseBooleanAttributes: true,//省略布尔属性的值 <input checked="true"/> ==> <input />
        removeEmptyAttributes: true,//删除所有空格作属性值 <input id="" /> ==> <input />
        removeScriptTypeAttributes: true,//删除<script>的type="text/javascript"
        removeStyleLinkTypeAttributes: true,//删除<style>和<link>的type="text/css"
        minifyJS: true,//压缩页面JS
        minifyCSS: true//压缩页面CSS
    };

    gulp.src("index.html")
        .pipe(htmlmin(options))
        .pipe(gulp.dest('min.html'));

});
```

