---
title: 调整图片
date: 2014-01-25 20:11:57
categories:
- 前端
tags:
- javascript
---

通常，我们可以给图片`<img>`设置一个 CSS 属性定义其高度和宽度，但是这样做缺乏灵活性，所以这当然不是一个很好地解决办法。

<!--more-->

## 通过CSS属性Max-width调整图片
想到了CSS的 max-width 属性，通过它把图片控制允许的在最大范围之内，而且是按比例缩放。这只要一行代码：

    img{
    	max-width: 100%;
    }

这一行对于大多数嵌入网页的视频也有效，所以可以这样写：

    img, object{
    	max-width: 100%;
    }

但是，老版本的IE不支持max-width,所以可以这样写：

    img{
    	width: 100%;
    }
    
    //ps：本人反对使用expression属性，诸如：
    img.max { 
    	width: expression(document.body.clientWidth > 200 ? "200px" : "auto");; 
    }

此外， windows 平台缩放图片时，可能会出现图像失真现象。这时，可以尝试使用IE的[专有命令](http://css-tricks.com/ie-fix-bicubic-scaling-for-images/)：

	img{
		-ms-interpolation-mode: bicubic;
	}

但是上述方式本人并不推荐，此前看到[CSS 与 HTML5 响应式图片](http://www.iyunlu.com/view/Front-end/70.html/)这篇文章，其中就有对图片缩放失真的处理可移步看一下。

## jQuery实现图片调整
以下是实现调整图片的代码，实际使用的时候修改为你所要控制的图片即可（这里是`#example img`）:

	$(window).bind("load", function() {
		// Img Resizing
		$('#example img').each(function() {
		var maxWidth = 200;//最大宽度（自定义）
		var maxHeight = 200;//最大高度（自定义）
		var ratio = 0; //比例
		var width = $(this).width();
		var height = $(this).height();
		if(width > maxWidth){
			ratio = maxWidth / width;
			$(this).css("width", maxWidth);
			$(this).css("height", height * ratio);
			height = height * ratio;
		}
	
		var width = $(this).width();
		var height = $(this).height();
		if(height > maxHeight){
			ratio = maxHeight / height;
			$(this).css("height", maxHeight);
			$(this).css("width", width * ratio);
			width = width * ratio;
		}
		});
	});

## 原生JavaScript实现
如下是原生JavaScript实现的代码：

	function resizeImage(id, maxHeight, maxWidth) {
	    // assumes the image is already loaded
	    // assume no height and width is being forced on it yet
	    var img = document.getElementById("res-img");
	    var height = img.height || 0;
	    var width = img.width || 0;
	    var maxHeight = 400;
	    var maxWidth  = 200;
	    if (height > maxHeight || width > maxWidth) {
	        var aspect = height / width;
	        var maxAspect = maxHeight / maxWidth;
	        if (aspect > maxAspect) {
	            img.style.height = maxHeight + "px";
	        } else {
	            img.style.width = maxWidth + "px";
	        }
	    }
	}

以上代码，假设图片已经正确加载了，并且没有强制设定高和宽。原生的javascript的实现也是相当简单的，这里只对`id="res-img"`的缩放。这里我介绍一个便捷的判断图片是否正确加载的小技巧ps：懂一点儿js调试技巧即可。

	//一小段jQuery代码
	$('img').load(function() {
		console.log('image load successful');
	});	

## 参考文献
- [FLUID IMAGES](http://unstoppablerobotninja.com/entry/fluid-images/)
- [自适应网页设计（Responsive Web Design）](http://www.ruanyifeng.com/blog/2012/05/responsive_web_design.html/)
- [IE Fix: Bicubic Scaling for Images](http://css-tricks.com/ie-fix-bicubic-scaling-for-images/)
- [stackoverflow Js Image Resize](http://stackoverflow.com/questions/170624/javascript-image-resize)

## 结语
总结以上，个人认为通过CSS处理是比较合适的方案，后面两种通过Javascript脚本动态改变的方法在特定情况下可能比较合适，但是做个 Demo 就可以看出其浏览效果不流畅（毕竟是要等到页面加载完成以后脚本再处理图片 ）。所以，个人推荐通过CSS解决图片缩放调整问题。希望各前端高手能指出文中的不足和错误！同时期待您有好的方法拿出来分享，谢谢！