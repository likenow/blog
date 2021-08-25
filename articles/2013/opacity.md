---
title: CSS透明度属性
date: 2013-11-21 20:05:16
categories:
- CSS
tags:
- 透明
---

透明网页的运用已包括在设计方式，在表现方式上，在交互性，3D效果，视差等效果上都有华丽的表现。通过设置透明效果来形成对比，避免遮挡图片的重要部分已经是一项关键的手法。本文就HTML元素如何通过CSS实现各浏览器的透明度做详细的介绍。

<!--more-->

## 浏览器的支持情况及CSS实现
CSS3中定义了`{opacity: | inherit;}`来声明元素的透明度。其实IE很早就通过滤镜手法实现了透明效果，而且现在主浏览器都已经支持 opacity 属性。

    /************IE************/
    -ms-filter:"progid:DXImageTransform.Microsoft.Alpha(Opacity=50)";
    /*IE4 to IE9 supported the extended form progid:DXImageTransform.Microsoft.Alpha(Opacity=xx)*/
    filter:alpha(opacity=50); /* ie8 and lower  */
    -ms-filter: "alpha(opacity=xx)"; /*ie8*/
    -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(opacity=30)"; /*ie8*/
    
    /************FireFox************/
    -moz-opacity:0.5; /* old mozilla browser like netscape  */
    
    /************Webkit内核早期浏览器************/
    -khtml-opacity: 0.5; /* for really really old safari */ 
    
    /************标准************/
    opacity: 0.5; /* css standard, currently it works in most modern browsers*/

在IE8中，-ms-filter 是 filter 的别名，两者区别在于 -ms-filter 的属性值需要被引号引起来，而 filter 则不必，而在IE8之前的IE版本中， filter 属性值必须不被单引号或双引号包围。注意：IE中的HTML元素要实现透明度，必须开启了 layout ，这样的元素有仅可读的属性 haslayout ，属性值：true

### 火狐浏览器

> History: Prior to Mozilla 1.7 (Firefox 0.9) the -moz-opacity property was implemented in a non-standard (inherited) way. With Firefox 0.9 the behavior changed and the property was renamed to opacity. Since then -moz-opacity was supported just as an alias for opacity.
Gecko 1.9.1 (Firefox 3.5) and later do not support -moz-opacity and support for MozOpacity in javascript was removed in Gecko 13. By now, you should be using simply opacity.

大体意思就是讲火狐早期版本（这里提到了Mozilla 1.7 (Firefox 0.9)）中的 -moz-opacity 是非标准的实现， -moz-opacity 是作为 opacity 的别名。在稍后的版本中（这里提到了Gecko 1.9.1 (Firefox 3.5)）就不再使用 -moz-opacity

### Webkit内核的早期浏览器

> Similar to -moz-opacity, -khtml-opacity has been dead since early 2004 (release of Safari 1.2).

同火狐浏览器一样，-khtml-opacity 自2004年 Safari 1.2 发布后就自挂东南枝了。

## 结语
本文就这样结束了，总结一下：简单介绍了透明度设计，然后详述了如何通过CSS实现HTML元素透明。这里不得不说因为IE浏览器的特立独行让我们不得不考虑兼容性方面的问题。最后，提前感谢您能够之初文章的不足，期待与您交流更多这方面的知识。