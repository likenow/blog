---
title: jQuery 制作垂直方向的 Accordion 效果
date: 2014-02-22 20:14:47
categories:
- 前端
tags:
- javascript
---

个人觉得，人们创造出这种手风琴设计无非是想在更小的空间呈现出更多的内容，再加之一些炫丽的修饰更加迷人眼。

<!--more-->

## HTML框架
这一块没什么可说的，随便用什么标签。接下来是jQuery

    <ul id="accordion">
    	<h1 class="accordion-toggle"><a href="#">手风琴一</a></h1>
    	<li class="accordion-content db">	
        <ul>
    		<li><a href="#">1手风琴第1部分</a></li>
    		<li><a href="#">1手风琴第1部分</a></li>
    		<li><a href="#">1手风琴第1部分</a></li>
    	</ul>
    	</li>
    	<h1 class="accordion-toggle"><a href="#">手风琴二</a></h1>
    	<li class="accordion-content">
    		<ul>
    			<li><a href="#">2手风琴第2部分</a></li>
    			<li><a href="#">2手风琴第2部分</a></li>
    			<li><a href="#">2手风琴第2部分</a></li>
    		</ul>
    	</li>
    	<h1 class="accordion-toggle"><a href="#">手风琴三</a></h1>
    	<li class="accordion-content">		
    		<ul>
    			<li><a href="#">3手风琴第3部分</a></li>
    			<li><a href="#">3手风琴第3部分</a></li>
    			<li><a href="#">3手风琴第3部分</a></li>
    		</ul>
    	</li>
    </ul>

## jQuery代码
在jQuery的强大支持下，仅需几行代码就搞定了。这里用到了 not 方法和 next 方法及和jQuery中常用动画 slide 。比较简洁明了，这里就不再赘述。

    jQuery(document).ready(function($) {		
    	$('#accordion').find('.accordion-toggle').click(function(){
    			var $next = $(this).next();
    			$next.slideToggle('fast');
    			$('.accordion-content').not($next).slideUp('fast');
    	});
    });

鄙人做的demo比较丑陋，这里献上关于手风琴（Accordion）效果的相关网站：
- [Accordion](http://www.p51labs.com/accordion/)
- [精心挑选的12款优秀 jQuery 手风琴效果插件和教程](http://www.cnblogs.com/lhb25/archive/2012/08/21/jquery-accordion-tutorials.html)
- [jQuery powerSwitch万能slide(切换)插件](http://www.zhangxinxu.com/wordpress/?p=3758)
- [30 jQuery Accordion Menus, Tutorials And Best Examples](http://www.1stwebdesigner.com/freebies/jquery-accordion-menus-tutorials/)
- [jQuery UI Accordion](http://jqueryui.com/accordion/)

## 结语
鄙人晓得这个效果实现已经有很多碉堡的插件，我这里介绍手风琴效果的实现仅作为自己的学习笔记以及希冀队看到此文章的人有所帮助，同时希望前端高手能指出文中的不足和错误！同时期待您分享好的方法，谢谢！