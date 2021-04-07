---
title: 图片替换
date: 2013-09-24 19:50:51
categories:
- CSS
tags:
- CSS
---

我们很多时候更希望通过图片生动形象的展示一个内容，但又不希望无法显示图片的客户端什么东西也看不到。所以，严格意义上的图片替换技术是让可以呈现图片的客户端呈现出光鲜亮丽的图片，让不能呈现图片的客户端同样可以获得有效的信息。

<!--more-->

## 第一种处理方案

### 浏览器支持

- windows
    - IE5.0及以上
    - Opera6及以上
    - Firefox
    - Chrome
- mac
    - IE5.2
    - Safari
    - Firefox

### CSS样式：

    .replace{
        height: 0;
        padding:100px 0 0 0;/*使用零高度来隐藏文本，内边距100px是图片的高度*/
        overflow: hidden;
        background: url(example.gif) no-repeat;/*该图像高度100px;*/
    }    

## 第二种处理方案    

### 浏览器支持

- windows
    - IE5.5及以上版本
    - Opera6及以上版本
    - Firefox
    - Chrome

- mac
    - IE5.2
    - Safari
    - Firefox
    - Chrome
    
### CSS样式：

    .replace{
        text-indent: -9999px;/*是文字飞到九霄云外*/
        background: url(sample-image.gif) no-repeat;
        height: 100px;/*图片高度*/
    }

### 以上两方案的HTML代码

    <h3 class="replace">这段文字将被替换。</h3>

以上两种方案，阅读器可以读出文字但是，在用户禁用图像后仍是一片空白，显然不尽如人意。

## 第三种处理方案

### 浏览器支持
- windows
    - IE5.5及以上版本
    - Opera6及以上版本
    - Firefox
    - Chrome

- mac
    - IE5.2
    - Safari
    - Firefox
    - Chrome

### HTML代码部分：

    <h3 class="replace" id="rep">该文字将被替换<span></span><h3>

### CSS样式部分：

    .replace{
        position: relative;
        margin: 0px;
        padding: 0px;
        overflow: hidden;/*溢出隐藏*/
    }
    .replace span{
        display: block;
        position: absolute;
        top: 0px;
        left: 0px;
        z-index: 1;/*针对于Opera5/6*/
    }
    #rep,#rep span{
        height:100px;
        width: 300p;
        background: url(example.png);
    }

此方法，解决了浏览器禁用图片后空白页面的可访问性问题。但是，覆盖着东西一定要滴水不漏才好，如果图像透明就露馅了。

## 第四种方案
### 浏览器支持
IE6/7对他支持的并不完美，Chrome早期的版根干本不鸟

### HTML代码部分：

    <h3 class="replace"><span>该文字将被替换</span><h3>

### CSS样式部分：

    .replace{
        height:32px;
        width:32px;
        background: url(example.png);
    }
    .replace span{
        display: block;
        width: 0;
        height: 0;
        font-size: 0;
        overflow: hidden;
    }

## 第五种方案
### 浏览器支持
支持所有主流浏览器哦！

### HTML代码部分：

    <h3 class="replace">该文字将被替换<h3>

### CSS样式部分：

    .replace{
        width: 32px;
        height：79px;
        background: url(example.png);
        font-size: 1px;/*字体大小设为1，掩人耳目*/
        color: white;/*颜色随机应变，“变色龙”称号赐予它*/
    }

## 综述
以上介绍的五种方案是比较成熟的方法，可以运用到实际的项目中。好的方法总是人想出来的，经过学习优秀案例自己也会总结出一种属于自己的方法。如果你有好的方法那就拿出来一起分享吧！