---
title: 转《你可能不需要jQuery！使用原生JavaScript进行开发》
date: 2014-07-19 23:08:30
categories:
- 前端
tags:
- javascript
- jQuery
---

很多的 JavaScript 开发人员，包括我在内，都很喜欢 [**jQuery**](http://www.cnblogs.com/lhb25/category/277997.html)。因为它的简单，因为它有很多丰富的插件可供使用，和其它优秀的工具一样，jQuery 让我们开发人员能够更轻松的开发网站和 Web 应用。

然而，另一方面，作为前端开发的基础框架，[**jQuery**](http://www.cnblogs.com/lhb25/category/277997.html) 包含大量的兼容性代码和扩展功能，其中有很多在你的整个项目中可能都不会用到。其实如果你只是针对现代浏览器，很多功能使用原生的 JavaScript 就可以实现，即使是拖后腿的低版本 IE 浏览器，兼容性也是很容易处理的。

<!--more-->

您可能感兴趣的相关文章

- [期待已久的2013年度最佳 jQuery 插件揭晓](http://www.cnblogs.com/lhb25/p/best-jquery-plugins-of-2013.html)
- [小伙伴们都惊呆了！8个超炫的 Web 效果](http://www.cnblogs.com/lhb25/p/8-amazing-codepen-demos.html)
- [10大流行的 Metro UI 风格 Bootstrap 主题](http://www.cnblogs.com/lhb25/archive/2013/04/11/10-metro-bootstraps-ui-templates.html)
- [推荐35款精致的 CSS3 和 HTML5 网页模板](http://www.cnblogs.com/lhb25/archive/2012/08/03/5-creative-html5-and-css3-templates.html)
- [精选12款优秀 jQuery Ajax 分页插件和教程](http://www.cnblogs.com/lhb25/archive/2012/08/08/ajax-jquery-pagination-plugin-tutorial.html)

![](https://images0.cnblogs.com/blog/36987/201402/211526460708413.png)

下面就带大家一起看看在 IE 浏览器环境中如果使用原生 **JavaScript** 代码实现 jQuery 中的功能。如果你打算自己开发一个小的基础框架，可以好好参考一下这些代码的实现。 

## Ajax Post
### jQuery: 
```javascript
$.ajax({
  type: 'POST',
  url: '/my/url',
  data: data
});
```
### IE8+: 
```javascript
var request = new XMLHttpRequest();
request.open('POST', '/my/url', true);
request.send(data);
```
## Ajax Get
### jQuery: 
```javascript
$.ajax({
  type: 'GET',
  url: '/my/url',
  success: function(resp) {
 
  },
  error: function() {
 
  }
});　
```
### IE8+: 
```javascript
request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
 
request.onreadystatechange = function() {
  if (this.readyState === 4){
    if (this.status >= 200 && this.status < 400){
      // Success!
      resp = this.responseText;
    } else {
      // Error :(
    }
  }
}
 
request.send();
request = null;
```

## 加载 JSON
### jQuery: 
```javascript
$.getJSON('/my/url', function(data) {
 
});
```
### IE8+: 
```javascript
request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
 
request.onreadystatechange = function() {
  if (this.readyState === 4){
    if (this.status >= 200 && this.status < 400){
      // Success!
      data = JSON.parse(this.responseText);
    } else {
      // Error :(
    }
  }
}
 
request.send();
request = null;
```

## 淡入效果
### jQuery: 
```javascript
$(el).fadeIn();　
```
### IE8+: 
```javascript
function fadeIn(el) {
  var opacity = 0;
 
  el.style.opacity = 0;
  el.style.filter = '';
 
  var last = +new Date();
  var tick = function() {
    opacity += (new Date() - last) / 400;
    el.style.opacity = opacity;
    el.style.filter = 'alpha(opacity=' + (100 * opacity)|0 + ')';
 
    last = +new Date();
 
    if (opacity < 1) {
      (window.requestAnimationFrame && requestAnimationFrame(tick)) || setTimeout(tick, 16);
    }
  };
 
  tick();
}
 
fadeIn(el);
```
## 显示和隐藏
### jQuery: 
```javascript
$(el).show();
$(el).hide();
```
### IE8+: 
```javascript
el.style.display = '';
el.style.display = 'none';
```
## 添加 Class
### jQuery: 
```javascript
$(el).addClass(className);
```
### IE8+: 
```javascript
if (el.classList)
  el.classList.add(className);
else
  el.className += ' ' + className;
```

## 插入 HTML
### jQuery: 
```javascript
$(el).before(htmlString);
$(parent).append(el);
$(el).after(htmlString);
```
### IE8+: 
```javascript
el.insertAdjacentHTML('beforebegin', htmlString);
parent.appendChild(el);
el.insertAdjacentHTML('afterend', htmlString);
```

## 获取子节点
### jQuery: 
```javascript
$(el).children();
```
### IE8+: 
```javascript
var children = [];
for (var i=el.children.length; i--;){
  // Skip comment nodes on IE8
  if (el.children[i].nodeType != 8)
    children.unshift(el.children[i]);
}
```

## 循环节点
### jQuery: 
```javascript
$(selector).each(function(i, el){
 
});
```
### IE8+: 
```javascript
function forEachElement(selector, fn) {
  var elements = document.querySelectorAll(selector);
  for (var i = 0; i < elements.length; i++)
    fn(elements[i], i);
}
 
forEachElement(selector, function(el, i){
 
});
```

## 清空节点
### jQuery: 
```javascript
$(el).empty();
```
### IE8+: 
```javascript
while(el.firstChild)
  el.removeChild(el.firstChild)
```

## 过滤节点
### jQuery: 
```javascript
$(selector).filter(filterFn);
```
### IE8+: 
```javascript
function filter(selector, filterFn) {
  var elements = document.querySelectorAll(selector);
  var out = [];
  for (var i = elements.length; i--;) {
    if (filterFn(elements[i]))
      out.unshift(elements[i]);
  }
  return out;
}
 
filter(selector, filterFn);
```
## 查找元素
### jQuery: 
```javascript
$(el).find(selector);
$('.my #awesome selector');
```
### IE8+: 
```javascript
el.querySelectorAll(selector);
document.querySelectorAll('.my #awesome selector');
```
## 获取属性、HTML或者文本
### jQuery: 
```javascript
$(el).attr('tabindex');
$(el).html();
$('<div>').append($(el).clone()).html();
$(el).text();
```
### IE8+: 
```javascript
el.getAttribute('tabindex');
el.innerHTML
el.outerHTML
el.textContent || el.innerText
```
## 判断是否包含某个 css class
### jQuery: 
```javascript
$(el).hasClass(className);
```
### IE8+: 
```javascript
if (el.classList)
  el.classList.contains(className);
else
  new RegExp('(^| )' + className + '( |$)', 'gi').test(el.className);　
```
## 选择器匹配
### jQuery: 
```javascript
$(el).is('.my-class');
```
### IE8+: 
```javascript
var matches = function(el, selector) {
  var _matches = (el.matches || el.matchesSelector || el.msMatchesSelector || el.mozMatchesSelector || el.webkitMatchesSelector || el.oMatchesSelector);
 
  if (_matches) {
    return _matches.call(el, selector);
  } else {
    var nodes = el.parentNode.querySelectorAll(selector);
    for (var i = nodes.length; i--;)
      if (nodes[i] === el) {
        return true;
    }
    return false;
}
 
matches(el, '.my-class');
```

## 前一个节点
### jQuery: 
```javascript
$(el).prev();
```
### IE8+: 
```javascript
// prevSibling can include text nodes
function previousElementSibling(el) {
  do { el = el.previousSibling; } while ( el && el.nodeType !== 1 );
  return el;
}
 
el.previousElementSibling || previousElementSibling(el);
```
## 后一个节点
### jQuery: 
```javascript
$(el).next();
```
### IE8+: 
```javascript
// nextSibling can include text nodes
function nextElementSibling(el) {
  do { el = el.nextSibling; } while ( el && el.nodeType !== 1 );
  return el;
}
 
el.nextElementSibling || nextElementSibling(el);
```
## 外部高度
### jQuery: 
```javascript
$(el).outerHeight()
```
### IE8+: 
```javascript
function outerHeight(el, includeMargin){
  var height = el.offsetHeight;
 
  if(includeMargin){
    var style = el.currentStyle || getComputedStyle(el);
    height += parseInt(style.marginTop) + parseInt(style.marginBottom);
  }
  return height;
}
 
outerHeight(el, true);
```

## 外部宽度
### jQuery: 
```javascript
$(el).outerWidth()
```
### IE8+: 
```javascript
function outerWidth(el, includeMargin){
  var height = el.offsetWidth;
 
  if(includeMargin){
    var style = el.currentStyle || getComputedStyle(el);
    height += parseInt(style.marginLeft) + parseInt(style.marginRight);
  }
  return height;
}
 
outerWidth(el, true);
```
## 判断是否数组
### jQuery: 
```javascript
$.isArray(arr);
```
### IE8+: 
```javascript
isArray = Array.isArray || function(arr) {
  return Object.prototype.toString.call(arr) == '[object Array]';
}
 
isArray(arr);
```
## 数组转换
### jQuery: 
```javascript
$.map(array, function(value, index){
 
})
```

### IE8+: 
```javascript
function map(arr, fn) {
  var results = []
  for (var i = 0; i < arr.length; i++)
    results.push(fn(arr[i], i))
  return results
}
 
map(array, function(value, index){
 
})
```


类似的还有很多很多，可以参考这里：<http://youmightnotneedjquery.com/>。

您可能感兴趣的相关文章

- [精心挑选的美轮美奂的 jQuery 图片特效插件](http://www.cnblogs.com/lhb25/archive/2012/08/13/jquery-image-effects-plugins.html)
- [精心挑选的优秀jQuery Ajax分页插件和教程](http://www.cnblogs.com/lhb25/archive/2012/08/08/ajax-jquery-pagination-plugin-tutorial.html)
- [精心挑选的优秀 jQuery 文本特效插件和教程](http://www.cnblogs.com/lhb25/archive/2012/08/06/jquery-text-effects-plugins.html)
- [8款非常棒的响应式 jQuery 幻灯片插件推荐](http://www.cnblogs.com/lhb25/archive/2012/01/31/8-responsive-jquery-slider-plugins.html)
- [精心挑选12款优秀 jQuery 手风琴插件和教程](http://www.cnblogs.com/lhb25/archive/2012/08/21/jquery-accordion-tutorials.html)

 

本文链接：[你可能不需要 jQuery！编写原生的JavaScript代码](http://www.cnblogs.com/lhb25/p/you-might-not-need-jquery.html)[
](http://www.cnblogs.com/lhb25/p/smarter-sticky-elements-with-jquery.html)

编译来源：[梦想天空 ◆ 关注前端开发技术 ◆ 分享网页设计资源](http://www.cnblogs.com/lhb25/)

### 推荐博文

- [Web 前端工程师和设计师必读精华文章推荐](http://www.cnblogs.com/lhb25/p/must-read-links-for-web-designers-and-developers-volume-12.html)
- [酷！15个精美的 HTML5 单页网站作品欣赏](http://www.cnblogs.com/lhb25//lhb25/archive/2011/07/28/html5-awesome-single-page-sites-inspiration.html)
- [炫！35个让人惊讶的 CSS3 动画效果演示](http://www.cnblogs.com/lhb25//lhb25/archive/2011/11/22/best-awesome-css3-animation-demos.html)
- [赞！30个与众不同的优秀视差滚动效果网站](http://www.cnblogs.com/lhb25//lhb25/archive/2012/03/02/30-mind-blowing-parallax-scrolling-effect-websites.html)
- [靓å！25个优秀的国外单页网站设计作品欣赏](http://www.cnblogs.com/lhb25//lhb25/archive/2012/01/13/25-outstanding-single-page-website-designs.html)
- [帅！8个惊艳的 HTML5 和 JavaScript 特效](http://www.cnblogs.com/lhb25//lhb25/archive/2011/08/09/awesome-html5-and-javascript-effects.html)
- [顶！35个很漂亮的国外 Flash 网站作品欣赏](http://www.cnblogs.com/lhb25//lhb25/archive/2011/06/27/35-exclusive-rainbow-colored-flash-websites.html)
- [哇！34个漂亮网站和应用程序后台管理界面](http://www.cnblogs.com/lhb25//lhb25/archive/2011/08/24/outstanding-admin-panels-part-one.html)



[本博客新站点 ◆ 前端里 ◆ 欢迎围观：）](http://www.yyyweb.com/go/web)

作者：[山边小溪](http://home.cnblogs.com/lhb25) 
主站：[yyyweb.com](http://yyyweb.com/) 记住啦：） 
欢迎任何形式的转载，但请务必注明出处。