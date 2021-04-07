---
title: 了解按钮
date: 2013-10-23 20:01:41
categories:
- 前端
tags:
- 按钮
---

本篇将通过按钮讨论一下，按钮的CSS实现以及按钮通常绑定的事件。虽然本人不擅长设计但是也会提及一下简单的按钮设计中不成文的规定。

<!--more-->

## 按钮设计须知
毕竟按钮是与用户的直接碰撞，所以按钮起码不应让用户感到反感
按钮要与网站统一风格，冷不丁冒出一个突兀的方块会吓到人！
按钮要让用户收到最起码的反馈信息。**交互设计**这个词应该不陌生吧，这是交互设计领域里的内容，这里不做详细介绍。
按钮要有足够的对照，要主次分明。这当然是为了提升网页的浏览感觉，符合用户通常的习惯，会让用户有良好的心情继续浏览。

## 按钮的CSS实现
这里的实现方式只是个人觉得比较普遍的实现方式，抛砖引玉，如果您用更优秀的按钮实现，提前感谢您拿出来分享。
### CSS代码部分

    .btn{
       display: block;
       width: 200px;/*视情况而定，具体项目中一般都会有宽度限定*/
       height: 32px;/*视情况而定，具体项目中一般都会有宽度限定*/
       line-height: 32px;/*垂直居中不是必需的*/
       padding: 10px 0;
       text-align: center;/*居中不是必需的*/
       font-size: 16px;
       border: 1px solid #333;/*看情况添加边框*/
       border-radius: 5px;/*CSS3实现圆角效果，如果要兼容低版本的浏览器应另辟蹊径*/
       cursor: pointer;
       background: #eee;
    }

该CSS实现实在是再普通不过了，我曾见到过一些优秀的按钮实现，它们可以图文混排、高度与宽度适应里面的文字、高光投影效果等这里不再赘述

## 按钮通常绑定的事件举例
平时有没有遇到这样的菜单按钮，单击弹出下拉菜单，再单击收起。接下来就介绍一下它的实现
### HTML代码部分

    <div class="menu">
      <a href="#" class="btn">+</>
      <div class="sub-menu">
        <ul>
    	<li><a href="#">menu-list1</a></li>
    	<li><a href="#">menu-list2</a></li>
    	<li><a href="#">menu-list3</a></li>
    	<li><a href="#">menu-list4</a></li>
    	<li><a href="#">menu-list5</a></li>
    	<li><a href="#">menu-list6</a></li>
        </ul>
      </div>
    </div>    

### CSS代码部分

    .menu{
    	position: relative;
    	clear: both;
    	float: right;
    }
    .btn{
    	display: block;
    	width: 100px;
    	height: 28px;
    	line-height: 28px;
    	text-align: center;
    }
    .menu a.hover,
    .menu a:hover{
    	background: #bb0f98;
    	color: #333;
    }
    .sub-menu{
    	display: none;
    	position: absolute;
    	width: 228px;
    	background: #bb0f98;
    	padding: 16px;
    	right: 0;
    	top: 28px;
    	z-index: 3;
    }

### javascript代码部分

    $(function(){
      $(.menu a).click(function(event){
      /*单击事件*/
        event.stopPropagation();
        /*阻止冒泡事件在jQuery库下兼容性就不是问题了。注：IE下是cancelBubble*/
        if( $(this).hasClass('hover')){
          $(this).removeClass('hover');
          /*条件判断是否具有hover类,移除hover类*/      
          $('.sub-menu').slideUp(200,function(){
          /*jQuery的slideUp效果收起菜单列表*/
            $('this').removeAttr('style');
          /*移除slideUp产生的style="display: none;"*/
          });
        }else{
          $(this).addClass('hover');
          $('.sub-menu').slideDown(200);
         /*否则，添加hover类，并滑下菜单列表*/
        }
      });
    })    

## 总结
最近个人对这种按钮处理遇到的比较多，所以顺手记一下。全文对按钮的设计、CSS实现以及一点儿JS功能做了介绍。代码很糙更谈不上美观，错误的地方感谢您能提出来。