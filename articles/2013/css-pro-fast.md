---
title: 常用CSS属性速记
date: 2013-10-02 12:49:44
categories:
- CSS
tags:
- CSS
---

要成为一个前端工程师，了解CSS的属性自不必多说。这里我将写一篇关于常用的几个CSS属性简写的文章。

<!--more-->

我们经常会看到:

- background
- margin
- padding
- border
- font
- list-style
- transition

几个常用的CSS属性的简写形式举例
​    
    background: #ccc url('bg.png') no-repeat fixed center;
    
    margin: 2px 3px 4px 5px;
    
    padding: 5px 6px 7px 8px;
    
    border: 1px solid #333;
    
    font: italic small-caps bold 15px/30px Helvetica,sans-serif;
    
    list: circle inside url(list-circle-image.png);
    
    transition: width 2s linear 1s;

针对以上代码示例，我将对每个属性做具体解释。

## BACKGROUND
背景属性，用来定义元素的背景效果

- background-color ，背影色`#ccc`
- background-image ，背景图片`bg.png`
- background-repeat ，不平铺
- background-attachment ，背景相对视窗固定
- background-position ，位于中央

## MARGIN，PADDING，BORDER
这三个属性在之前的盒模型的文章中已经做过详细的分析，这里就不再赘述。如果您还不太熟悉可以看一下[盒模型]()这篇文章。

## FONT
CSS字体属性定义字体的样式设置包括：

- font-style ，字体倾斜
- font-variant ，小型大写字母
- font-weight ，粗体
- font-size/line-height ，字体大小15像素/行高30像素
- font-family ，字体HELVETICAL以及后备字体SANS-SERIF
- Caption
- icon
- menu
- message-box
- small-caption
- status-bar

## LIST
CSS列表属性的样式设置包括：

- list-style-type ，圆圈
- list-style-position ，内嵌
- list-style-image ，引用图片

## TRANSITION
这是CSS3转换属性，通过CSS3的这一属性我们可以在不使用Flash动画和JavaScripts的情况下定义变换时的效果。其属性如下：

- transition-property ，指定CSS属性名应用变换
- transition-duration ，定义变换的时长
- transition-timing-function ，描述如何计算一个变换的速度，默认是ease
- transition-delay ，定义变换何时开始，默认是从零开始。则上述代码示例解释为：宽度变换延续2s，linear效果，延迟1s。

## 参考文献

- [w3schools](http://www.w3schools.com/)
- [w3school](http://www.w3school.com.cn/)
- [w3s](http://www.w3s.com.cn/)

## 总结
本篇重点讲解了4个CSS常用属性的速记技巧。分别是

- background
- font
- list
- transition
- margin,padding,and border属性

希望这些速记技巧可以对你的编程效率有所提高。