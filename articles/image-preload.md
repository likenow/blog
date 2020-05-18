---
title: 图片预加载
date: 2014-01-23 20:11:40
categories:
- 前端
tags:
- javascript
---

图片预加载功能将在页面加载的时候加载那些图片并将其存储在浏览器的缓存里面。这样当用户浏览器页面的时候，漂亮而且流畅，没有延迟，从而可以给用户带来流畅的浏览体验。

<!--more-->

## CSS和JavaScript实现预加载功能
通过CSS就可以实现图片的预加载，代码如下：

	#preload-01 { 
		background: url(http://domain.tld/image-01.png) no-repeat -9999px -9999px; 
	}
	#preload-02 { 
		background: url(http://domain.tld/image-02.png) no-repeat -9999px -9999px; 
	}
	#preload-03 { 
		background: url(http://domain.tld/image-03.png) no-repeat -9999px -9999px; 
	}	

将这3个ID应用到需要的元素中，我们便可通过CSS的 background 属性将图片预加载到屏幕外的背景上。只要这些图片的路径保持不变，当它们在Web页面的其他地方被调用时，浏览器就会在渲染过程中使用预加载（缓存）的图片。
看过神飞的[纯CSS图片预加载](http://www.qianduan.net/pure-css-image-preloader.html/)一文，讲的就是通过CSS的 background 属性来实现预加载。他讲到了CSS3中的**多背景图片属性**。所以，你可以这样用（如果支持CSS3），代码如下：

	#preload{
		background: url(pre-img1.jpg), url(pre-img2.jpg)···;
	}

大家应该也注意到了，纯CSS实现图片预加载比较简单，但是使用该方法加载的图片会同页面的其他内容一起加载，增加了整体的加载时间。所以，改进一下延迟预加载直到页面加载完毕就好多了。代码如下：

    // better image preloading @ http://perishablepress.com/press/2009/12/28/3-ways-preload-images-css-javascript-ajax/
    function preloader() {
    	if (document.getElementById) {
    		document.getElementById("preload-01").style.background = "url(http://domain.tld/image-01.png) no-repeat -9999px -9999px";
    		document.getElementById("preload-02").style.background = "url(http://domain.tld/image-02.png) no-repeat -9999px -9999px";
    		document.getElementById("preload-03").style.background = "url(http://domain.tld/image-03.png) no-repeat -9999px -9999px";
    	}
    }
    function addLoadEvent(func) {
    	var oldonload = window.onload;
    	if (typeof window.onload != 'function') {
    		window.onload = func;
    	} else {
    		window.onload = function() {
    			if (oldonload) {
    				oldonload();
    			}
    			func();
    		}
    	}
    }
    addLoadEvent(preloader);

代码比较明确，每加载一个图片都需要创建一个变量，如：`img1 = new image();`，及图片源地址声明，如：`img3.src = "../path/to/image-003.gif";`参考该代码你可以根据需要预加载更多的图片。

## 仅使用JavaScript
以下是一段Js代码，使用该代码到你的网站只需要简单的编辑要加载图片的路径与名称即可，展示：

    function preloader() {  
        if (document.images) {  
            var img1 = new Image();  
            var img2 = new Image();  
            var img3 = new Image();  
            img1.src = "http://domain.tld/path/to/image-001.gif";  
            img2.src = "http://domain.tld/path/to/image-002.gif";  
            img3.src = "http://domain.tld/path/to/image-003.gif";  
        }  
    }  
    function addLoadEvent(func) {  
        var oldonload = window.onload;  
        if (typeof window.onload != 'function') {  
            window.onload = func;  
        } else {  
            window.onload = function() {  
                if (oldonload) {  
                    oldonload();  
                }  
                func();  
            }  
        }  
    }  
    addLoadEvent(preloader);  

上述代码思路比较明确
- 第一步：预加载图片
- 第二步：延迟预加载图片加载
- 第三步：调用。

## jQuery实现图片预加载
虽然目前有很多可以实现图片预加载功能的插件，但是有时候我们没有必要使用一个有几百行几十行代码，功能很强大的插件。其实很多时候我们仅仅写几行代码就可以简单的实现图片预加载在功能。下面是几行代码:

	$.preloadImages = function() {
		for(var i = 0; i").attr("src", arguments[i]);
		}
	}
	$(document).ready(function() {
		$.preloadImages("pre-img-1.jpg","pre-img-2.jpg");
	});

## Ajax实现图片预加载
AJAX 光看名字就觉得“高大上”了不是吗？它是 Asynchronous JavaScript And XML 的首字母缩写。所以，这样对AJAX字面上的了解是：异步 JavaScript 及 XML。AJAX 并不是一种新的编程语言，而仅仅是一种新的技术，它可以创建更好、更快且交互性更强的 web 应用程序。AJAX 使用 JavaScript 在 web 浏览器与 web 服务器之间来发送和接收数据。通过在幕后与 web 服务器交换数据，而不是每当用户作出改变时重载整个 web 页面，AJAX 技术可以使网页更迅速地响应。所以，通过AJAX来实现图片预加载功能简直就是无可挑剔啊！好吧，我们来实现一下：

	window.onload = function() {  
	    setTimeout(function() {  
	        // XHR to request a JS and a CSS  
	        var xhr = new XMLHttpRequest();  
	        xhr.open('GET', 'http://domain.tld/preload.js');  
	        xhr.send('');  
	        xhr = new XMLHttpRequest();  
	        xhr.open('GET', 'http://domain.tld/preload.css');  
	        xhr.send('');  
	        // preload image  
	        new Image().src = "http://domain.tld/preload.png";  
	    }, 1000);  
	};  

以上代码，预加载了“preload.js”、“preload.css”和“preload.png”。1000毫秒的超时是为了防止脚本挂起，而导致正常页面出现功能问题。接着我们看一下如何用JavaScript来实现加载过程：

	window.onload = function() {  
	  
	    setTimeout(function() {  
	  
	        // reference to   
	        var head = document.getElementsByTagName('head')[0];  
	  
	        // a new CSS  
	        var css = document.createElement('link');  
	        css.type = "text/css";  
	        css.rel  = "stylesheet";  
	        css.href = "http://domain.tld/preload.css";  
	  
	        // a new JS  
	        var js  = document.createElement("script");  
	        js.type = "text/javascript";  
	        js.src  = "http://domain.tld/preload.js";  
	  
	        // preload JS and CSS  
	        head.appendChild(css);  
	        head.appendChild(js);  
	  
	        // preload image  
	        new Image().src = "http://domain.tld/preload.png";  
	  
	    }, 1000);  
	  
	};

这里通过DOM创建三个元素来实现三个文件的预加载。正如上面提到的那样，使用AJAX，加载文件不会应用到加载页面上。

## 参考资料：
- [3 Ways to Preload Images with CSS, JavaScript, or Ajax](http://perishablepress.com/3-ways-preload-images-css-javascript-ajax/)
- [Creating a CSS Image Preloader](http://www.devirtuoso.com/2009/07/creating-a-css-image-preloader/)
- [纯CSS图片预加载](http://www.qianduan.net/pure-css-image-preloader.html/)

## 结语
以上，分享了四种实现图片预加载功能的方法（ps: *最近的项目里也遇到了图片预加载这个问题，上面的几种方法尝试了，在 chrome 里都可以，network 面板也有显示加载，但是在手机平台上就是没法加载，最后没办法做的 sprite 解决的。这块还得多做测试。*）期待您有好的方法拿出来分享，谢谢！