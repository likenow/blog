---
title: Node-WebKit开发HTML5本地桌面应用
date: 2014-12-16 14:31:26
categories:
- 前端
tags:
- Node-WebKit
---

## 前言

上一次，介绍了使用前端开发技术栈（`html/css/js`）通过` PhoneGap `构建出不同平台（苹果、安卓、塞班等）的应用软件。这次要说的[node-webkit](https://github.com/rogerwang/node-webkit) 这个项目开始于 2011 年，是一个让开发者可以用 `Web` 技术，也就是 `HTML5`、`CSS` 和` JavaScript` 来编写桌面程序的应用框架。它能让你把**HTML5**应用打包成本地桌面应用或游戏安装到`Windows`、`Linux`或`Mac`系统中。目前在` github` 上，`node-webkit` 有将近` 15000+` 个` Star`，`1600+` 个 `Fork`，可以说是一个非常流行的开源软件。项目发起人，**王文睿**介绍了目前使用 `node-webkit` 开发的一些典型应用，大致可以分成这么几类，

- 桌面开发` IDE`，比如 [Light Table](http://www.lighttable.com/)
- 游戏，比如 [Brandon Must Die!](http://www.brandonmustdie.com/)
- 媒体类应用，比如 [Popcorn Time](http://popcorntime.io/)
- 工具软件，比如 [Haroopod](http://pad.haroopress.com/)
- 企业内部应用，比如数据显示应用
- 终端应用，比如 `POS` 机。

可以看出，领域非常丰富，其中也不乏一些设计精良体验优美的应用。可以说，很大程度上，这得益于目前硬件的性能提升和 Web 的发展，动画、图形、音频、视频、`2D`、`3D`、`WebGL` 等等，都让基于 `HTML5` 的应用开发充满了想象力。

<!--more-->

## node 和 webkit

### node

`Node` 是一个基于 `Google Chrome V8` 引擎的轻量级 `Javascript `运行平台。`Node` 采用 `C++ `编写，直接在操作系统的应用层上，支持很多系统级的 `API`，如文件操作、网络编程等，所以可以用来编写系统级应用或者服务端应用等。

### webkit

`Webkit` 是 `Web` 内容的渲染引擎。举个例子，一个网页由 `HTML、CSS 和 JavaScript` 组成，页面加载后会被语法分析器解析成树，然后交由` Webkit` 做渲染，这样才得到一个我们看到的页面。

### node-webkit

由于浏览器安全模型的缘故，`Webkit` 并不能访问到系统调用，而这却是桌面应用的必须。`Node` 本身支持系统级的 `API`，但是缺乏对于渲染的支持，所以王文睿在 2011 年就有了做 `node-webkit` 的想法，把两者揉合在一起，既能对接操作系统，又能提供用户交互。在做简单尝试用 `webkit-gtk` 和 `node `来做实验后，在 2012 年开始转向 `Chromium`，从一开始使用的 `Chromium Embeded Framework（CEF）`到后面完全基于` Chromium`。

一下为[开始 NODE-WEBKIT 前，你应该知道的](http://yedingding.com/2014/07/28/node-webkit-intro.html)这篇文章的引文，感觉非常好，没有必要自己编，直接拿过来了。

> Node 是一个事件驱动的框架，消息处理基于 libuv 实现。Webkit 的 JS 引擎也是事件驱动的，是 Chrome 自己实现的 MessagePump。所谓事件驱动，简单说就是当某个事件发生的时候，比如有用户输入进来，去执行对应的处理代码。 所以，node-webkit 统一了 node 和 webkit 中的事件处理方式，在一个消息循环中去处理 node 里和 webkit 里的不同事件。
>
> 解决了事件处理统一的问题后，node 和 webkit 的揉合还需要互相能访问。node-webkit 里面存在着两个 context，node context 和 webkit context，互相访问也就是 Context 能互通，访问各自的对象。所以在 node-webkit 初始化的时候，node context 会被注入到 WebKit 里面。之后，Webkit context 就可以访问到 node context 了。
>
> 同时，node-webkit 对 Webkit 的安全模型也做了一些修改。在 node-webkit 中，有两种不同的 frames，node frame 和 normal frame[1](http://yedingding.com/2014/07/28/node-webkit-intro.html#fn:1)。熟悉 Web 开发的朋友应该知道，对不同的站点发起一个 AJAX 请求会返回跨域错误，也就是 [Same-origin Policy](http://en.wikipedia.org/wiki/Same_origin_policy)。node-webkit 中的 normal frame，跟浏览器中的一样，仍然有这个限制。但是在 node frame 中，将不再有这个限制，所有的 node frame 中共享同一个 security token，包括 node context 和 webkit context，跨域访问被允许，也就能访问到 node context 中的对象。所以作为应用开发者，对于不受信任的用户行为输入一定要做保护，防止因为这个造成一些破坏性行为，或者放入 normal frame 中。
>
> ![Chromium Layers](http://yedingding.com/images/node-webkit-intro/chromium-layers.jpg?1477738241)Chromium Conceptual Application Layers
>
> 值得注意的是，尽管 Chrome 本身对于 HTML5 的支持已经比较完备了，但是有些特性是在浏览器层即上图的 Browser 实现，而 node-webkit 中 webkit 只到了 Content Layer 即上图的 WebContent，node-webkit 本身的实现等于浏览器层。所以，node-webkit 对于一些 HTML5 特性还缺乏支持，比如 Web Notification[2](http://yedingding.com/2014/07/28/node-webkit-intro.html#fn:2)。不过，目前 Chromium 团队正在把这些 HTML5 特性的支持解耦移动到 Content Layer，这个对于 node-webkit 来说绝对是个非常好的消息。
>
> 如果你细心的看上图的话，你会发现浏览器层还有 UI 绘制部分。node-webkit 也是一样，实现了直接绘制原生 UI 控件的 API，比如菜单、系统托盘、剪贴板等。这部分实现，的确也是主要参考了 Chromium 的跨平台 UI 实现。
>
> 目前 node-webkit 的正式版是 0.10.0，基于 Node v0.11.13 和 Chromium 35.0.1916.113。node-webkit 的大版本更新策略是跟着 Chromium 的版本升级而升级，比如 0.10.0 中的 10。如果 node-webkit 本身有更新或者 node 版本有更新，对应升级的是第三个数字，也就是 0.10.0 中的最后一个 0。
>
> 如果你想了解最多 node-webkit 的信息，可以听听我们这期 [Teahour 节目](http://teahour.fm/2014/07/22/node-webkit-with-rogerwang.html)。如果你想开发跨平台的桌面应用，那么官方的 [Wiki](https://github.com/rogerwang/node-webkit/wiki) 绝对是个很好的开始。
>
> 1. 对于 node-webkit 的安全模型，具体可以参考 https://github.com/rogerwang/node-webkit/wiki/Security, 了解如何使用这两种不同的 Frame。 [↩](http://yedingding.com/2014/07/28/node-webkit-intro.html#fnref:1)
> 2. 目前已经有 [PR](https://github.com/rogerwang/node-webkit/pull/1951) 实现了 Notification，相信不久会在 node-webkit 正式版中被支持。



## 实际操作一波（Windows操作系统下）

### 基本开发流程

1. 一个 HTML 工程，这里简单点，直接一个`index.html`
2. 配置文件 `package.json`

```json
/*
除了name与main这两个属性外，还有很多其他有用的属性可以配置，比如指定应用的图标，显不显示浏览器的工具栏，指定浏览器的初始大小等等，具体的配置参数文档可看这里
https://github.com/rogerwang/node-webkit/wiki/Manifest-format
*/
{
    "name": "test",
    "main": "index.html" // 入口文件
}
```

3. 把`package.json`与`index.html`所在文件夹压缩为`app.zip`

4. 然后把`app.zip`这个文件的扩展名改变为 `app.nw`

5. 然后下载一个`windows`版本的`node-webkit`，解压后得到一个文件夹

6. 之后我们之前得到的`app.nw`这个文件就可以用`nw.exe`来执行了，直接把`app.nw`拖到`nw.exe`上就可以了。

7. `node-webkit`登场，把`app.nw`这个文件跟`node-webkit`的环境文件一起打包成一个可执行文件：

   1. 打开`windows`的`cmd`，然后输入如下命令：

   ```
   copy /b nw.exe+app.nw app.exe
   ```

   注意文件路径要根据你的实际情况进行变动，这里假设`app.nw`放在了`node-webkit`的主文件夹里，然后输出的`app.exe`也会在这个文件夹里。

   执行命令后我们得到了 `app.exe` 这个可执行文件。

8. 到了这步，我们已经得到了`app.exe`这个文件，但如果只有`app.exe`这个文件还是不够的，这个可执行文件的运行还需要几个`dll`文件的支持。其中
   1.  `nw.pak` 与` icudt.dll `这个两个文件是必须要的。
   2. `ffmpegsumo.dll `文件是媒体支持文件，如果你的`html`页面中用到了`<video>`或`<audio>`或其它与媒体相关的东西，则必须带上这个文件。
   3. `libEGL.dll` 和` libGLESv2.dll `这个两个文件则是使用webGL或GPU必须要的

9. 最后把上述文件放在一个文件夹，我们得到的就是这样一个文件夹：

```json
app.exe
ffmpegsumo.dll
icudt.dll
libEGL.dll
libGLESv2.dll
nw.pak
```

10. 执行`app.exe`就可以运行我们的demo了。大功告成？

### 追求完美

但我们大多数人想的是给用户一个`exe`文件，用户就可以使用了，不用再附带一些其他文件。

嗯，所以我们还可以把`app.exe`跟其他的文件再打包一次，做这步我们需要一个软件叫[Enigma Virtual Box](http://enigmaprotector.com/en/aboutvb.html)，首先[下载](http://enigmaprotector.com/assets/files/enigmavb.exe)和安装这个软件，然后打开它。

1. 然后在`Enter Input File Name`那里输入我们的`app.exe`的路径
2. 在`Enter Output File Name`那里填写我们要把打包出来的可执行文件输出到哪里。
3. 最后是把除`app.exe`外的其它文件拖入到`Files`那里，遇到提示的话默认就可以了

最终用户只需要把`app_boxed.exe `文件交给用户，用户就可以运行了。

### 还不完美

`node-webkit`虽然方便，但有个很大的缺点是得到的可执行文件有点大就这个Demo我试了一下**40M+**，大家在可以在衡量利弊后决定使不使用。



## 参考文献

- [用HTML5打造本地桌面应用](http://www.webhek.com/post/html5-native-desktop-app.html)
- [NW.js-GitHub](https://github.com/nwjs/nw.js)
- [开始 NODE-WEBKIT 前，你应该知道的](http://yedingding.com/2014/07/28/node-webkit-intro.html)
- [Creating Desktop Applications With node-webkit](https://strongloop.com/strongblog/creating-desktop-applications-with-node-webkit/)
- [用node-webkit把web应用打包成桌面应用](https://www.cnblogs.com/2050/p/3543011.html)
- [Manifest-format](https://github.com/nwjs/nw.js/wiki/Manifest-format)