---
title: 总结JavaScript文件的加载
date: 2013-12-29 20:10:14
categories:
- 前端
tags:
- javascript
---

文章将盘点 JavaScript 的加载方法以及性能分析。

<!--more-->

## 放在<script>和</script>之间或通过SRC外链

    /*<script>标签之间*/
    <script type="text/javascript">
    	alert("hello world");
    </script>
    /*外链*/
    <script type="text/javascript" src="example.js"></script>

直接在 Javascript 代码放在标记对`<script>`标签之间或者通过 src 属性外链是比较传统的方式了，当然放置的位置有两处。一处是：放在 head 标签内，很明显如果文件很大下载文件会延迟整个页面的生成速度；另一处是：放在整个页面的最底部等待最后加载这样修正了第一种放置方式的弊端。

## Document.write方式
`document.write()`会将其字符串参数连接起来，然后将结果字符串插入到文档中调用他的脚本元素的位置。当脚本执行结束，浏览器解析生成的输出并显示它。应用示例代码：

    <script type="text/javascript">//<![CDATA[
        document.write('<script type="text/javascript" src="test.js"></script>');//]]></script>
        <script type="text/javascript">//<![CDATA[
        	document.write('<script type="text/javascript">');
        	document.write('alert(2);')
        	document.write('alert("我是" + tmpStr);');
        	document.write('</script>');
        	//]]></script>
    <script type="text/javascript">//<![CDATA[
        alert(3);
        //]]></script>
    
    /*test.js*/
    var tmpStr = 1;
    alert(tmpStr);

`document.write()`，但是会把输出写入到脚本文档所在的位置，浏览器解析完`documemt.write()`所在文档内容后，继续解析`document.write()`输出的内容，然后在继续解析HTML文档。浏览器输出是：1、2、我是1、3。

## 延迟加载脚本
脚本的执行只在默认情况下是同步和阻塞的。`<script>`标签可以有 deferasync 属性，这可以在支持她们的浏览器里改变脚本的执行方式。

	<script defer src="deferred.js"></script>
	<script async src="async.js"></script>

defer 和 async 属性都像是在告诉浏览器链接进来的脚本不会使用`document.write()`，也不会生成文档内容，以此浏览器可以在下载脚本时继续解析和渲染文档。defer 属性使浏览器延迟脚本的执行，直到文档的载入和解析完成，并可以操作。async 属性使得浏览器可以尽快的执行脚本，而不用在下载脚本时阻塞文档解析。如果 defer 和 async 属性同时存在，async 属性优先，defer 属性被忽略。注意，延迟的脚本会按照它们在文档里的出现顺序执行。而异步脚本在它们载入后执行，顺序不一定按照原来的顺序执行（无序）。

## 动态加载脚本
### JavaScript方法

	function loadjs(script_filename) {
	    var script = document.createElement('script');
	    script.setAttribute('type', 'text/javascript');
	    script.setAttribute('src', script_filename);
	    script.setAttribute('id', 'coolshell_script_id');
	 
	    script_id = document.getElementById('coolshell_script_id');
	    if(script_id){
	        document.getElementsByTagName('head')[0].removeChild(script_id);
	    }
	    document.getElementsByTagName('head')[0].appendChild(script);
	}
	 
	var script = 'http://coolshell.cn/asyncjs/alert.js';
	loadjs(script);

该函数会动态的载入脚本——脚本载入到文档中，成为正在执行的JavaScript程序的一部分，既不是通过Web页面内联包含，也不是来自Web页面的静态引用。
### jQuery方法：

	$(document.createElement("script")).attr("src","test.js").appendTo("head");
	或
	$("<script type='text/javascript' src='test.js' />").appendTo("head");

但是上述方式并不理想。为此，jQuery提供了`$.getScript()`方法直接加载`.js`文件。

	$(function(){
		$('#send').click(function(){
			$.getScript('test.js');
		});
	})

该示例代码，当单击`id="send"`的按钮后，动态加载 `test.js` 文件。

## 结语
以上总结了 JavaScript 文件的加载，对于性能优化方面我再提几点：
1. 推荐将所有的`<script>`标签放到页面底部，也就是闭合标签之前，这能确保在脚本执行前页面已经完成了渲染
2. 推荐合理地合并脚本。页面中的`<script>`标签越少，加载也就越快，响应也越迅速。无论是外链脚本还是内嵌脚本都是如此。
3. 采用无阻塞下载 JavaScript 脚本的方法。本文重点讲解了JavaScript文件的加载,相信从中可以找到好的加载方法。
4. 如果动态加载的话，一般就是最后一种。现在也有一些模块加载器，例如 RequireJS 和玉伯他们的 SeaJS ，就是用这个 JS 来根据页面的情况，动态加载其他需要的 JS 并且增加依赖性有关的东西，可以了解一下。
好了，这是所有的内容了，希望大家看过后能对 Javascript 的载入有更深入地了解。同时，也希望各前端高手能指出文中的不足和错误！谢谢