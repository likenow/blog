---
title: CSS实现元素居中
date: 2013-09-23 16:51:29
categories:
- CSS
tags:
- CSS
---

在网页布局中经常需要使元素**居中**。本篇文章将总结一下CSS实现元素居中的方法。

<!--more-->

## 水平居中
### 第一种方法：

    /*第一种写法*/
    margin: 0 auto;
    /*第二种写法*/
    margin: 0 auto 0 auto;
    /*第三种写法*/
    margin-top: 0;
    margin-right: auto;
    margin-bottom: 0;
    margin-left: auto;

**注意：**设置`margin`为`auto`时，块元素必须有明确的`width` ，否则占满整行无法居中。

### 第二种方法：

    .container{
        height: 200px;
        width: 200px;
        background: #eee;
        margin: 10px auto;
        position: relative;
    }
    .inner-container{
        height: 50px;
        width: 50px;
        background: #333;
        position: absolute;
        left: 75px;/*水平方向：(200-50)/2*/
    }

上述，对内盒子使用了`position: absolute;`外围盒子使用了`position: relative;`把内盒子封印在自己体内免得他出来祸害人。关键是对内盒子设置了`left: 75px;`该值的设定使盒子水平居中显示。

### 第三种方法：

    .container{
        height: 200px;
        width: 70%;
        background: #eee;
        margin: 10px auto;
        position: relative;
    }
    .inner-container{
        height: 50px;
        width: 50px;
        background: #333;
        position: absolute;
        left: 50%;
        /*水平方向：负左边距 50/2*/
        margin: 0 0 0 -25px;
    }

该例子同上只不过把父容器盒子的宽度设成了**百分比**，相应的我们发现给内盒子应用了`·left: 50%;`以及负的左边距。

### 第四种方法：

    .container{
        height: 200px;
        width: 70%;
        background: #eee;
        margin: 10px auto;
        position: relative;
    }
    .inner-container{
        height: 50px;
        width: 70%;
        background: #333;
        position: absolute;
        left: 50%;
        /*水平方向：负左边距 70%/2*/
        margin: 0 0 0 -35%;
    }

该段代码给容器宽度统统应用了百分比值。但是原理还是相同的。

## 垂直居中
下述六种方法都是对如下**HTML**的样式编辑：

    <div class="container">
        <div class="float-container"></div>
        <div class="inner-container"></div>
    </div>

### 第一种方法：

    .container{
        height:250px;
    }
    .float-container{
        float:left;
        height: 50%；
        width: 100%;
        margin-bottom: -50px;
    }.inner-container{
        clear: both;
        height: 100px;
    }

该方法，我们在内容器上方放置一个浮动的容器并且给他父元素高度的`50%`，旨在让它占据外容器高度的一半，然后根据内容器的高度确定负外边距从而使内容器居中显示。对子容器设定高度之前我们先要清除浮动。

### 第二种方法：

    .container{
        display: table;
    }
    .inner-container{
        display: table-cell;
        vertical-align: middle;
    }

上述方法，首先使外容器以表格显示`display: table;`然后他就拥有了类似表格的特性。再使内容器以单元格显示并垂直居中(`vertical-align: middle;`（表格支持它）)从而实现了垂直居中。但是，此方法到*IE8BEAT*版本时任然不支持。

### 第三种方法：

    .container{
        height: 200px;
        width: 200px;
        background: #eee;
        /*水平/垂直方向：负左边距 200/2*/
        margin: -100px 0 0 -100px;
        position: absolute;
        left: 50%;
        top: 50%;
    }
    .inner-container{
        height: 50px;
        width: 50px;
        background: #333;
        position: absolute;
        left: 50%;
        top: 50%;
        /*水平/垂直方向：负左边距 50/2*/
        margin: -25px 0 0 -25px;
    }

这里外容器和内容器均使用了绝对定位及`left: 50%;` `top: 50%;`再通过负边距来实现居中。

### 第四种方法：

    .container{
        height: 200px;
        width: 200px;
        margin: 100px auto;
        background: #eee;
    }
    .h1{
        line-height: 200px;
        text-align: center;
    }

对于**单行的文字**，我们可以通过设置`line-height=height`和`text-align: center;`来实现文字垂直居中。

### 第五种方法：

    .container{
        height: 200px;
        line-height: 200px;
    }
    .container img{
        width: 100%;
        height: auto;
        vertical-align: middle;
    }

需要注意的是**外容器的高度要比图片的大**方能显现效果。

### 第六种方法：

    .container{
        background: #eee url(../images/example.png) no-repeat;
        background-position: center;
    }

这是对背景图片的定位，CSS给出了强大的布局。

## 结语

以上是我对CSS实现元素居中几种方法的总结。包含对块级元素和行内元素的居中（eg:单行文字，图片）以及背景图片的居中。同时您有本篇中未提到的居中的技巧希望留言中补充。