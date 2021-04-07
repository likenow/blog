---
title: 实现两列等高
date: 2013-12-03 20:07:33
categories:
- 前端
tags:
- javascript
---

文章将介绍四种两栏等高的实现方法。主要是通过两种手段：用CSS技巧来实现；通过脚本语言来实现。

<!--more-->

## 实现方法汇总
### HTML 代码

    <!doctype html>
    <html lang="en">
    <head>
    	<meta charset="UTF-8">
    	<title>Document</title>
    </head>
    <body>
    	<div class="header"></div>
    	<div class="wrapper">
    		<div class="content"></div>
    		<div class="sidebar"></div>
    	</div>
    	<div class="footer"></div>
    </body>
    </html>

### 通过CSS的position: absolute;实现
#### CSS 代码

    .content
    {
        float: left;
        margin-right: 50px;
        background-color: #FFE0F8;
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
    }
    .sidebar
    {
    	background-color: #E1FFE0;
        margin-left: 50px;
    }

上述代码实现必须是在事先知道 content 的高度大于 sidebar 的高度的情况下。如果相反则如下：

    .sidebar
    {
        float: left;
        margin-right: 50px;
        background-color: #FFE0F8;
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
    }
    .content
    {
    	background-color: #E1FFE0;
        margin-left: 50px;
    }

### 通过CSS的paddingmargin实现
#### CSS 代码

    .wrapper{
    	overflow:hidden; 
    	width:650px; 
    	margin:0 auto;
    }
    .content{
    	padding-bottom: 5000px; 
    	margin-bottom: -5000px; 
    	width:490px; background:#ff1; 
    	float:left; 
    	margin-right:10px;
    	height:400px;
    }
    .sidebar{
    	padding-bottom: 5000px; 
    	margin-bottom: -5000px; 
    	width:150px; 
    	background:#ccc; 
    	float:left;
    	height:800px;
    }

该方法中核心CSS代码部分的5000像素是个可变值，如果您的分栏高度不可能高度超过2000像素，您就可以设为2000像素，如果会超过3000像素，那么您要修改值为4000像素或是更高。父标签的`overflow:hidden`属性是必须的，否则会显示溢出的内容。弊端是：底部成了一个无底洞有没有？你在设置诸如`border-bottoom: 10px solid red;`的时候，边框在无底洞的底部无法出现在你的视野。该方法还有一个致命的缺陷，当用到锚跳转的时候，浏览器会把正文内容往上移动，这样就被切断了。

### 通过CSS的`display:table-cell`实现

    .wrapper{
    	display:table-row;
    }
    .sidebar{
    	display:table-cell; 
    	width:30%; 
    	padding:1.6%; 
    	background-color:#f5f5f5;
    }
    .content{
    	background-color:#f0f3f9;
    }

table表格布局似乎已经被CSS的光芒给覆盖了，但是不能忘记表格中的单元格一大特点————同一行列表元素都等高。所以，很多时候，我们需要等高布局的时候，就可以借助`display:table-cell`属性。然而，IE6/7浏览器不支持`display:table-cell`属性。所以，在浏览器兼容性这方面有一点瑕疵，但对于已经忽视IE6/7浏览器的人来说的确是个不错的选择。

### 通过脚本语言实现
#### jQ 代码

    ;(function($) {
    $(window).load( function() {
    	var lHeight = $("#content").height();
    	var rHeight = $("#sidebar").height();
    	if (lHeight > rHeight) {
          $('#sidebar').height(lHeight);
    	} else {
          $("#content").height(rHeight);
    	}
    });
    })(jQuery);

以上是jQuery的实现代码。需要注意的一个点是：`$(window).load()`。通过它来加载而不是`$(document).ready()`主要是考虑到 content 中会有图片所以，要等到图片加载完成以后来计算出高度，此时通过jQuery获取的高度更精确一些。

    lHeight = document.getelementByClassName('content');
    rHeight = document.getElementByClassName('sidebar');
     if(lHeight.clientHeight < rHeight.clientHeight){
    　　lHeight.style.height = rHeight .clientHeight + "px";
    }else{
    　　rHeight.style.height = lHeight.clientHeight + "px";
    }

以上是原生javascript实现方式，原理也比较明了：取得左右两边的高度，然后判断这个值，把大的值赋给小的就ok！

## 结语
文章汇总了四种实现两栏等高效果的方法，如果您有其他方式欢迎您能够不吝赐教。最后，提前感谢您指出文中补不足指出，本人收到立即改正。