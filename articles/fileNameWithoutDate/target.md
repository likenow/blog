---
title: 在新窗口打开链接
date: 2014-02-21 20:14:34
categories:
- 前端
tags:
- javascript
- html
- target
---

在此之前，我认为想要更好的实现在新窗口打开链接，首先对`<a>`及其属性 target 有一个较深刻的理解。

<!--more-->

## TARGET = “_blank”
这里推荐你到[A](http://www.htmlcodetutorial.com/linking/_A.html)和[TARGET](http://www.htmlcodetutorial.com/linking/_A_TARGET.html)来帮助你理解。以下内容是建立在你对`<a>`及其属性target清楚的前提下轻松实现在新窗口打开链接。所以，一上来我们先介绍一下HTML属性实现方式。

    <a href="http://kai-lee.com" target="_blank">该链接将在新窗口打开</a>

有人说：“给`<a>`设置`target="_blank"`在[Markup Validation Service](http://validator.w3.org/)下会*don’t validate*。”问题出在使用 Version of XHTML strict 文档声明方式。而且在W3C官方[HTML and XHTML Frequently Answered Questions](http://www.w3.org/MarkUp/2004/xhtml-faq#target)中就有解释：
> It wasn’t. XHTML 1.0 comes in three versions: strict, transitional, and frameset. All three of these were deliberately kept as close as possible to HTML 4.01 as XML would allow. XHTML 1.1 is an updated version of XHTML 1.0 strict, and no version of HTML strict has ever included the target attribute. The other two versions, transitional and frameset, were not updated, because there was nothing to update. If you want to use the target attribute, use XHTML 1.0 transitional.

那么在HTML5可以使用target="_blank"吗？答案是完全可以。

> W3.org HTML5 specification quote: “the target attribute for the a and area elements is no longer deprecated, as it is useful in Web applications, e.g. in conjunction with iframe.”

## JS实现方案
此部分介绍通过JS脚本实现，个人本着可以简洁就不自找麻烦的原则认为：如果在使用上述第一种方式可以实现就不必动用脚本。
### javascript方式
1. 内置javascript的方式通常是不推荐的

    `<a href="http://kai-lee.com" onclick="window.open(this.href); return false;" onkeypress="window.open(this.href); return false;">该链接将在新窗口打开</a>`

2. 当使用以下这段脚本时，兼容不同浏览器对 target 属性的不同处理方式,它将获得点击的链接的 rel 属性并且当单击的链接其`rel="external"`时将在新的窗口打开。    
  
    `<a href="http://kai-lee.com" rel="external">kailee</a>`


    document.onclick = function(e)
    {
      //兼容IE在内的主流浏览器使target正常工作
      var target = e ? e.target : window.event.srcElement;
      while (target && !/^(a|body)$/i.test(target.nodeName))
      {
        target = target.parentNode;
      }
      if (target && target.getAttribute('rel') && target.rel == 'external')
      {
        var external = window.open(target.href);
        return external.closed;
      }
    }

### jQuery方式
通过jQuery给获得`<a>`的`rel="external"`置为在新窗口打开链接。
​      
    <a href="http://kai-lee.com" rel="external">kailee</a>


    jQuery(document).ready(function($) {
    	$('a[rel="external"]')
    	.click( function() {
    	window.open( $(this).attr('href') );
    	return false;
    	});
    });

## 参考文献
- [Open External Links In New Window](http://css-tricks.com/snippets/jquery/open-external-links-in-new-window/)
- [External Links with CSS3 or jQuery](http://kovshenin.com/2011/external-links-with-css3-or-jquery/)
- [Target Only External Links](http://css-tricks.com/snippets/jquery/open-external-links-in-new-window/)
- [Use jQuery to make all offsite links open in a new window](http://www.electrictoolbox.com/jquery-open-offsite-links-new-window/)
- [HTML and XHTML Frequently Answered Questions](http://www.w3.org/MarkUp/2004/xhtml-faq#target)
- [Markup Validation Service](Markup Validation Service)
- [HTML Tutorials](http://www.htmlcodetutorial.com/)