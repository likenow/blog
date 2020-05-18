---
title: 自定义动画
date: 2013-10-20 20:01:20
categories:
- 前端
tags:
- javascript
---

jQuery库这里自不必多说，他有意识地保持少量核心特效，更多地发挥空间由开发者们来酌情添加更多动画。jQuery编写自定义动画其实很容易学习接受，它公开了`animate()`包装器方法。`animate()`允许为包装集中的元素应用自定义动画特效。

<!--more-->

## ANIMATE 语法

    animate(properties,duration,easing,callback)
    animate(properties,options)

将properties和easing参数指定的动画应用到包装集中的所有成员上。可指定回调函数，在动画完成时调用。另一种方式除了指定properties外，还指定了一组options。

- properties ，一个散列对象，指定动画结束时所支持的CSS样式应该达到的值。
- duration ，用于指定特效持续的时间，可以是毫秒数值，也可以是：slow/normal/fast之一。若省略或者设为0，则不会动画动过程。
- easing ，用于指定动画缓动特效持续，核心jQuery提供了两个缓动函数，分别注册为：linea/rswing。当然如果你安装了一些插件效果就更奇妙了。做这方面开发的开发人员，通过对时间应用数学公式带来一些有趣的变化。
- Callback ，回调函数，动画结束时调用。不传递参数，注意函数上下文是执行动画的元素。为使用动画的每个元素调用此函数。
- options ，使用一个散列对象来指定动画参数值，支持如下属性：
    - duration ，参考前边
    - easing ，参考前边
    - Complete ，complete是动画完成时候调用的函数；
    - queuestep ，queue的值如果为false则动画无需排队，立即运行；step在动画的每一步都会调用的回调函数，步骤序号和内部特效对象将被传入此回调函数。

现在是否想动手写一个动画效果呢？不要犹豫了，我们现在就写一个动画效果。

## 制作动画
### 缩放效果

    $('.animate').each(function(){//遍历每一个匹配元素
         $(this).animate({
           width: $(this).width() * 2,//指定目标值
           height: $(this).height() * 3
          },
          2000//设置持续时间
        );
    });

### 掉落效果动画    

    $('.animate').each(function(){
        $(this)
          .css('position','relative')//脱离普通文档流
          .animate(
            {
              opacity: 0,
              top: $(window).height() - $(this).height() - $(this).position().top
              //计算下降距离，这种方式避过了不太美观的滚动条的出现
            },
            2000,
            function(){$(this).hide();}//使用了hide()方法从显示器上删除元素
          );
    });

## 结语
到目前，我们都只是使用了一个动画方法，多个动画混合工作时效果会更酷炫。这里只是抛砖引玉，希望你可以迅速的做出自己的动画。当然，不少大神级的开发人员写了一些方便实用的插件，看他们源代码就觉得眼花缭乱，相信大神不是一天练成的菜鸟也有会飞的一天不是？学习优秀的代码，写自己的代码。最后，文中需要补充的地方提前感谢您能帮助指出。      