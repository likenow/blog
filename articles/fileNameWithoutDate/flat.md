---
title: 锁定DIV为视口大小
date: 2014-01-28 20:13:03
categories:
- 前端
tags:
- javascript
---

本文将通过 CSS 的 position 属性和 jQuery 动态改变`<div>`宽高两种方式实现了全屏显示效果。

<!--more-->

## 通过CSS转换DIV的宽高为视口大小
这段CSS代码有点儿拽着`<div>`的四个角使劲扯成为视口的大小的意思。

    div	{
        position: absolute;
        top:0;bottom:0; /* vertical center */
        left:0;right:0; /* horizontal center */
        background: pink;
    }

## jQuery动态调整DIV宽高为视口大小

    //把视口的宽高赋值给创建的全局变量
    var winWidth = $(window).width();
    var winHeight = $(window).height();
    //设置div的宽高
    $('div').css({
    	width: 'winWidth',
    	height: 'winHeight'
    });
    //确保视口变化时div依旧全屏展示
    $(window).resize(function(event) {
    	/* Act on the event */
    	$('div').css({
    		width: 'winWidth',
    		height: 'winHeight'
    	});
    });

这里提一下jQuery 事件  resize() 方法。resize() 方法触发 resize 事件，或规定当发生 resize 事件时运行某函数。这里是当调整浏览器窗口大小时`<div>`依旧全屏显示。(ps: resize 还要注意性能问题，不要处理太复杂的函数。如果太复杂的函数，你在缩放浏览器的时候，就比较卡顿，可以考虑函数节流的方法。)
在此之前，我曾经写过[实现两列等高](http://kai-lee.com/2013/12/03/equal-high/)一文，感觉两篇文章有联系可移步看一下。对于简单的使两个`<div>`等高可以这样：

	//对于不支持min-height属性的浏览器无效果
	$('.div').css('min-height', $(‘.main-div').height());
