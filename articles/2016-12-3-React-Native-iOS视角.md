---
title: React Native--iOS视角
date: 2016-12-03 20:56:25
tags:
- iOS
- rn
categories:
- React Native
---



## iOS React Native 初探

### 关注点：

1. 业务接入的便捷性; 

2. 开发效率与业务需求。



<!--more-->

#### 哪些业务 native 方式更好

- 低频变更，高频使用（业务层面，需具体分析）
- 多线程处理任务，比如：下载任务
- 数据库操作，比如：本地数据存储
- 设备硬件的调用，比如：摄像头、播放器



#### RN开发效率

#### RN优点

-  **跨平台开发**，同一段 Javascript 代码可以被用于 `iOS` 和 `Android` 两个平台。
- 快速迭代，符合业务方的迫切需求
- 性能上看 `Native >= React Native > Hybrid`

#### RN缺点

- 联调鸡肋，*红屏了解一下* ~：
- 依赖于 第三方，目前看来势头正旺
- 额外的学习成本，当然对于一部分人来说，可能不是问题



## 原生项目集成 React Native

问题：
1. `iOS` 如何将`React Native`集成到当前项目中？
2. 如何用 `React Native` 提供的原生组件实现界面？
3. 如何实现符合自己业务的原生组件？
4. 写完的 `JS` 如何打包给 `Native` 使用？
5. 集成到原生项目，如何处理项目中的统一导航和 `React Native` 提供的导航？

带着以上几个问题，可以更快的开始一个Demo。以后的文章里，我也会对以上问题进行解答。



## APP版本管理和发布调研

版本管理和发布是非常重要的基础功能模块，主要功能是将 `JS` 业务层及其引用的代码编译` link `好的 `JSBundle` 下载到` Native App `中。在此过程中，需要控制更新文件的大小以及失败情况的处理。

版本管理和发布负责：

- ` JSBundle` 资源管理（服务端）

- `JSBundle` 数据接口层（服务端）

- `JSBundle` `Native` 更新及管理层（`Native`）

  `JSBundle` 资源管理系统负责将相关 JS 业务层代码编译` link` 成 `JSBundle` 文件，并将相关更新写到一个数据缓存中心（例如 `Redis`）。当 `Native` 通过 `JSBundle` 数据接口层提供的接口获取对应的` JSBundle` 的信息时，数据接口层将从数据缓存中心查询数据并返回给` Native` 端。`Native `端为了提高用户体验，会对 `JSBundle` 进行缓存，用户访问相关页面的时候，先展示缓存，再访问接口，看是否有更新。

  `启动`-->`载入`-->`展示RN页面`-->`网络请求页面`-->`非/强制更新`-->`结束`



## 参考文献

- [React Native中文网](https://reactnative.cn/)

- [iOS 开发是否要采用 React Native?](https://juejin.im/post/5a1249f56fb9a0451b0425cd)