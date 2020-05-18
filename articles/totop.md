---
title: totop
date: 2013-09-30 19:54:38
categories:
- 前端
tags:
- javascript
---

当我们浏览网页的时候，往往会垂直滚动浏览更多内容。如果有这样一个按钮当你开始滚动页面的时候他浮现出来，单击一下可以回到网页顶部然后消失。你的设计里是否需要一个这样的设计呢？接下来我将通过jQuery实现它。

<!--more-->

## 准备工作
在开始之前，首先要了解jQuery的以下内容：
1. click事件
2. scroll事件
3. fadeIn（淡入效果）
4. fadeOut（淡出效果）
5. animate（自定义效果）
6. scrollTop（获取对滚动条顶部的偏移）

## click事件
`click([[data],fn])`触发每个匹配元素的click事件。这个函数会调用执行绑定到click事件的所有函数。参数：
- data供函数fn处理；fn在每一个匹配元素的click事件中绑定的处理函数。

## scroll事件
`scroll([[data],fn])`，在每个匹配元素的scroll事件中绑定一个处理函数。当滚动条发生变化时触发。参数：
- data供函数fn处理；fn在每一个匹配元素的scroll事件中绑定的处理函数。

## fadeIn/fadeOut
`fadeIn([speed],[easing],[fn])`，通过不透明度的变化来实现所有匹配元素的淡入（淡出）效果，并在动画完成后可选地触发一个回调函数。ps：该动画效果只调整元素的不透明度，元素的宽高不变。参数：
1. `speed[,fn]`，用来设定速度的`speed("slow","normal"or"fast")`或毫秒数`speed(1000)`；fn是在动画完成时执行的函数，每个元素执行一次。
2. `[speed],[easing],[fn]`。用来设定速度的`speed("slow","normal"or"fast")`或毫秒数`speed(1000)`；fn是在动画完成时执行的函数，每个元素执行一次；easing用来指定切换效果，默认是”swing”，可用参数”linear”。

## animate
`animate(params,[speed],[easing],[fn])`，用于创建自定动画函数。该函数关键在于指定动画形式及结果样式属性。这个对象中每个属性都表示一个可以变化的样式属性（eg：”height”、”top”或”opacity”）。推荐属性命名法——**骆驼式**，eg：*marginLeft*。参数：
1. `params,[speed],[easing],[fn]`。params：一组包含作为动画属性和终值的样式属性及其值的集合；用来设定速度的`speed("slow","normal"or"fast")`或毫秒数`speed(1000)`；fn是在动画完成时执行的函数，每个元素执行一次；easing用来指定切换效果，默认是”swing”，可用参数”linear”。
2. `params,options`。params：一组包含作为动画属性和终值的样式属性及其值的集合；options：动画的额外选项。如：speed – 设置动画的速度,easing – 规定要使用的 easing 函数,callback – 规定动画完成之后要执行的函数,step – 规定动画的每一步完成之后要执行的函数,queue – 布尔值。指示是否在效果队列中放置动画。如果为 false，则动画将立即开始,specialEasing – 来自 styles 参数的一个或多个 CSS 属性的映射，以及它们的对应 easing 函数

## scrollTop

`scrollTop([val])`，获取匹配元素相对滚动条顶部的偏移。ps：对可见和隐藏元素均有效。参数：val设定垂直滚动条的值。

## 综合运用得到一键置顶按钮
### 代码其实很简单，代码如下：

    /*一键置顶*/
    $('a[href="#"]').click(function($e) {
    	/* Act on the event */
    	$e.preventDefault();/*阻止链接的默认行为*/
    });
    $('.back-to-top').click(function() {
    	/* Act on the event */
    	$('html,body').animate({scrollTop:'0'}, 200);
    });
    $(window).scroll(function(){
    	var scrollH = document.documentElement.scrollTop + documentbody.scrollTop;
    	if( scrollH > 200 ){
    		$('.back-to-top').fadeIn(200);
    	}else{
    		$('.back-to-top').fadeOut(200);
    	}
    });

我们可以看到通过jQuery来实现真是小菜一碟。基本上就是选择包装集，然后在应用上述讲到的方法操作包装集。这样就ok了！如果您想马上试一下效果，你可以在你的样式文件里添加back-to-top类后把这段代码粘贴到你的js文件夹里就可以马上看到效果了。关于back-to-top类的写法这里就不赘述了，您可以尽情发挥。    	

## 结语
今天的内容就这么点儿，好吧总结一下：设计一个一键置顶按钮需要用到click、scroll、fadeIn、fadeOut、animate以及scrollTop。了解了基本的方法，当滚动条发生变化的时候（scroll获取相对于滚动条顶部偏移位置）我们定义了一个自定义动画效果 ，然后就搞定了。