---
title: JS异步加载、延时加载、按需加载
date: 2020-06-27 22:13:58
categories:
        - javascript
tags:
        - JavaScript
---

{% codeblock lang:js %}
// defer  async  动态创建script标签三种方式都可以实现JS代码的异步甚至按需加载。
{% endcodeblock %}

## defer适应于IE浏览器，async适应于非IE浏览器，而第三种动态创建dom的方式用的最多，稍后着重讲动态创建script标签的方式按需加载JS代码。

先讲一下异步加载的原理，当浏览器开始解析HTML文档的时候，遇到没有defer或者async关键字的script标签时，会立刻执行其内部的JavaScript代码（通俗点说：看到就执行），且后面的HTML文档的解析将被阻断（阻塞状态），直至该script标签的JavaScript代码解析并执行完毕，才会继续解析HTML文档并渲染页面。

反之，遇到有defer或者async关键字的script标签，将继续解析下面的HTML文档的同时异步加载（并行）该script标签src所指向的JavaScript代码（defer标签内可以写JavaScript代码，而async标签内不行）。但是async在异步加载的同时就可以执行其代码，defer关键字所指向的js代码的执行需要在所有HTML元素解析完成之后，DOMContentLoaded事件触发之前完成。

下面是带defer和async的script标签示例：

{% codeblock lang:js %}
// defer async关键字写在src前后都可以
<script defer src="test.js"></script>
<script async src="test.js"></script>

// 也可以写成下面这种方式（我相信脑袋没问题的话一般不愿意这么写）：
<script defer="defer" src="test.js"></script>

// 带defer的script标签内部书写JS代码的情况也是被允许的
<script defer="defer" src="test.js">console.log(this);</script>
{% endcodeblock %}


## 下面是摘自简书谋篇文章中对于异步加载过程的抽象图：

![async](http://image-i99.test.upcdn.net/i99-images/js-async.png)


## JS异步加载

下面来看看如何使用动态创建script标签的方式异步加载外部JS
{% codeblock lang:js %}
var script = document.createElement('script');
script.type = "text/JavaScript";
script.src = "test.js";

// 也可以在body中插入
document.head.appendChild(script);
test();
{% endcodeblock %}

假设test.js中有方法 function test() { console.log('test执行'); }
这时候打开控制台你会发现错的一塌糊涂，报错信息：ReferenceError: test is not defined
test is not defined? 居然报这么低级的test未定义错误？你没有看错，就是报未定义错误。
假设说上面五行代码分别为12345行，假设JS引擎执行这五行代码需要5毫秒的时间，假想状态下每行需要1毫秒的时间，但是在这个过程中惊悚的事情发生了，第三行的src所指向的JS文件是需要下载时间的，也就是说遇到src就开启一个新的线程，从发送请求到下载和加载完毕的时间早就远超下面两行代码执行所需要的时间。那么也就意味着test.js都还没下载过来，JS引擎已经把test()方法都执行完了，但是在执行test方法的时候发现找不到这个方法，所以给出了这么低级的未定义错误。

既然问题出来了，那么肯定有解决办法，script标签有个onload事件，当触发这个onload事件的时候就代表src指向的JS代码下载完了并且已经准备就绪。这个时候就可以正常调用其内部方法了。代码如下：

{% codeblock lang:js %}
script.onload = function () { test(); }
{% endcodeblock %}

但是事情远没有这么简单，往往好用的东西都会携带一个弊端，那就是不兼容，script.onload事件仅针对非IE浏览器有效，那就意味着还有一套对应着IE的处理方法。不知道微软是真的想保持个性呢，还是妄想垄断浏览器市场，但是二者都很搞笑。说个性，微软还真没啥个性，至少跟处处彰显个性的苹果没法比。说想垄断IE市场，背对W3C只能毁掉自己。

在IE上，script上有个readyState属性，在加载src指向的JS代码的过程中会动态的改变该属性的值，在最初，script.readyState = "loading"，在加载完毕之后该属性值会被改成"complete" 或 "loaded"。IE上面的onreadystatechange事件可以监听readyState的状态变化。那么对应的处理方法如下：

{% codeblock lang:js %}
script.onreadystatechange = function () {
    if (script.readyState == "complete" || script.readyState == "loaded") {
        test();
    }
}
{% endcodeblock %}

## 下面是完整的动态创建script标签方式异步加载外部JS的兼容写法代码示例：

{% codeblock lang:js %}
function asyncLoadJS(url, fn) {
        var script = document.createElement('script');
        script.type = "text/JavaScript";

        if (script.readyState) { // IE
            script.onreadystatechange = function () {
                if (script.readyState == 'complete' || script.readyState == 'loaded') {
                    fn();
                }
            }
        } else {
            script.onload = function () {
                fn();
            }
        }

        // 事件在于触发，万一src在还没读到readyState状态那句就已经加载完毕了，那就无法触发onreadystatechange事件

        // 所以写在事件触发代码下面，确保事件可以正常触发
        script.src = url;

        document.head.appendChild(script);
}

// 参数2传欲调用的函数，但是直接传函数引用将报错，比如直接传test函数引用，JS引擎无法识别test到底是个什么东西

// 传递一个匿名函数，在匿名函数内执行test()可以解决此问题

asyncLoadJS("test.js", function () {test();});
{% endcodeblock %}

## 上面还有个DOMContentLoaded事件现在补充一下：

DOMContentLoaded事件只能用addEventListener来绑定，比如JQuery的document.ready()方法就是通过监听DOMContentLoaded来实现的，代码如下：

{% codeblock lang:js %}
document.addEventListener('DOMContentLoaded', function () { console.log(document.readyState); }
{% endcodeblock %}

当然网上也有一些“大法”，比如window.onload大法，每次写东西，window.onload = function () { ...... }，这玩意每次都要等网页所有的资源都加载完毕才会触发这个事件。尤其是图片资源，特别浪费时间，等加载完毕可能需要数秒钟的时间，在这个时间内，JS代码一直是无法工作的。用户根本没耐心等待那么久，基本上两三秒没内容展示，用户就关掉了。所以要尽量避免使用window.onload。
