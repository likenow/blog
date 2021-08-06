---
title: CocoaPods的简单使用
date: 2016-04-03 15:32:47
tags:
- Cocoapods
- 工具
categories:
- 工具
---



## CocoaPods的简单使用

安装方式异常简单 , Mac 下都自带 `ruby`，使用 `ruby` 的 `gem` 命令即可下载安装：

```shell
$ sudo gem install cocoapods
$ pod setup
```

<!--more-->

详见[官网](https://cocoapods.org/)

使用 `CocoaPods`,使用时需要在项目目录新建一个名为` Podfile` 的文件，以如下格式，将依赖的库名字依次列在文件中即可

```ruby
platform :ios
pod 'JSONKit',       '~> 1.4'
pod 'Reachability',  '~> 3.0.0'
```

关于详细的podfile的编写,也可以在[官方文档中进行查看](https://guides.cocoapods.org/syntax/podfile.html).然后你将编辑好的 `Podfile` 文件放到你的项目根目录中，然后在该目录下执行如下命令即可：

```shell
pod install
```

现在，你的所有第三方库都已经下载完成并且设置好了编译参数和依赖，使用 `CocoaPods` 生成的 `.xcworkspace` 文件来打开工程，而不是以前的` .xcodeproj` 文件。使用`install` 会优先安装`Podfile.lock`文件中的内容,而不会主动更新`Podfile.lock`文件.而执行`pod update`方法会主动获取最新的库,并更新`Podfile.lock`文件.

pod的命令有:

```ruby
+ cache      Manipulate the CocoaPods cache
+ init       Generate a Podfile for the current directory.
+ install    Install project dependencies to Podfile.lock versions
+ ipc        Inter-process communication
+ lib        Develop pods
+ list       List pods
+ outdated   Show outdated project dependencies
+ plugins    Show available CocoaPods plugins
+ repo       Manage spec-repositories
+ search     Search for pods.
+ setup      Setup the CocoaPods environment
+ spec       Manage pod specs
+ trunk      Interact with the CocoaPods API (e.g. publishing new specs)
+ try        Try a Pod!
+ update     Update outdated project dependencies and create new Podfile.lock
```



## 在项目中使用和管理CocoaPods

- 首先,代码管理上,只上传`Podfile`,来控制库的版本,不上传`Pods`文件夹和`.xcworkspace`.
- 建立私有`repo`库,将所有的第三方库纳入私有库中管理,这样编译时,就可以不用连接外网编译了.
- **Podfile.lock 应该加入版本控制**，保证小伙伴们的依赖库版本一致，防止出现难以发现的 bug。另外，在初始创建项目的时候就应该加入版本控制。
- Podfile 的语法最好能尽量严谨，最好**指定明确的依赖库版本。**





### 参考

- [Cocoapods 官方文档](https://guides.cocoapods.org/)