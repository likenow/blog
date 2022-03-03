---
title: iOS开发工具XCode的使用与遇到的问题
date: 2015-09-21 21:32:02
tags:
- XCode
- 工具
categories:
- 工具
---



## Xcode 12 (Version 12.0 beta 6 (12A8189n))

### imcompatible block pointer types sending xxx

解决：

![image-20200924134121709](../../assets/image-20200924134121709.png)

### Xcode 12, building for iOS Simulator, but linking in object file built for iOS, for architecture arm64

解决：

> I've seen quite a bit of weird behavior with frameworks, I think due to changes to the simulators to support Apple silicon. My temporary workaround is, in my app/extension targets, to add "arm64" to the Excluded Architectures build setting when building for the simulator (as your preview appears to be trying to do), and setting "Build Active Architecture Only" to No for all schemes. Might be worth a try.

<img src="../../assets/image-20200907112056864.png" alt="image-20200907112056864" style="zoom:80%;" />

> If you are using custom `XCConfig` files, you can simply add this line for excluding simulator architecture.
>
> ```undefined
> EXCLUDED_ARCHS[sdk=iphonesimulator*] = arm64
> ```
>
> **Then**
>
> You have to do the same for the **Pod project** until all the Cocoa pod vendors are done adding following in their **Podspec**.
>
> ```rb
> s.pod_target_xcconfig = { 'EXCLUDED_ARCHS[sdk=iphonesimulator*]' => 'arm64' }
> s.user_target_xcconfig = { 'EXCLUDED_ARCHS[sdk=iphonesimulator*]' => 'arm64' }
> ```
>
> You can manually add the *Excluded Architecture* in your Pod project's **Build Settings**, but it will be overwritten when you use `pod install`.
>
> In place of this, you can add this snippet in your `Podfile`. It will write the necessary **Build Settings** every time you run `pod install`.
>
> ```rb
> post_install do |installer|
>   installer.pods_project.build_configurations.each do |config|
>     config.build_settings["EXCLUDED_ARCHS[sdk=iphonesimulator*]"] = "arm64"
>   end
> end
> ```



<img src="../../assets/image-20200907112237568.png" alt="image-20200907112237568" style="zoom:80%;" />

解决：

```
Keychain Access
->
Right-click on login
->
Lock & unlock again
->
Clean Xcode project
->
Make build again
```





## `$(SRCROOT)` 和 `$(PROJECT_DIR)`

[What the different between SRCROOT and PROJECT_DIR?](https://stackoverflow.com/questions/36323031/what-the-different-between-srcroot-and-project-dir/40739356)

> Exchangeable in practice, while the documentation makes these subtle theoretical distinctions:
> 1. SRCROOT
> Directory path. Identifies the directory containing the target’s source files: contains the path to the project file that defines the target.
> SOURCE_ROOT is an undocumented alias to SRCROOT
> 2. PROJECT_DIR
Identifies the directory containing the project (.xcodeproj)
$(PROJECT_DIR)/build is used as the create the default value for:
> Intermediate Build Files Path OBJROOT
Build Products Path SYMROOT
Typically these paths are not set per target, but are set per-project or per-user.
> 3. PROJECT_FILE_PATH
Identifies the project itself.
Equivalent to $(PROJECT_DIR)/$(PROJECT_NAME).xcodeproj
> Conceptually different (#1 is about the project which defines the target while #2 is about the project independently of any target), they are always pointing to the same location since you are, in essence, always building a target.
> References
> - Xcode 8.3 Build Settings reference
> - Xcode Build System Guide (Retired 2016-09-29)

![](../../assets/2015-xcode-1.png)



## Search Paths

常用路径 `path` ，就是下面三种啦：

- `Framework Search Paths`
   附加到项目中的`framework` 的搜索路径。
- `Library Search Paths`
   附加到项目中的第三方`Library`的搜索路径。
- `Header Search Path`
   头文件的搜索路径。
- `User Header Search Paths`
   只有在`Always Search User Paths`为`Yes`时才会被搜索。



## Library Search Paths / Header Search Paths

> I'm looking for a documentation, too. But I made the experience, that `$(inherited)` can be used to inherit build settings from the project level to the target level. When you define library or header search paths at the project level you can use `$(inherited)` in the target build settings to use these search paths in the search paths of the project targets.

> 注意：
>
> - 路径中有空格必须要加引号
>
> - 双引号的作用是如果在路径中有空格，可以识别该路径。
> - 没有双引号但是路径中有空格，它会自动变成两个路径。
> - 多个路径可以用空格隔开
> - 如果取的是绝对路径那么工程移到别的地方就有可能导致运行出错。所以要改成相对路径


```
举例：
// 情况1：
$(inherited) "$(SRCROOT)/.a文件所在的文件名"
// 情况2：如果有多个.a文件格式就像这样 
$(inherited) "$(SRCROOT)/xxxx" "$(SRCROOT)/xx"
```



### “Header Search Paths” vs. “User Header Search Paths” in Xcode?

> Use the `*User Header Search Paths*` for paths you want searched for `#include "..."` and use the `*Header Search Paths*` for `#include <...>`. Of course, if you check the option to` *Always Search User Paths*`, then `#include <...>` will also work for the user paths.

> `< >` is for frameworks -- `.a` and `.frameworks` "libraries" -- and it doesn't matter if it's a system framework, one of your own or a 3rd party (like Boost.) `" "` is for project headers -- .h files that are part of the set of files being compiled. Hope that helps clarify. 



## $(inherited)

> [What is $(inherited) in Xcode's search path settings?](https://stackoverflow.com/questions/15343122/what-is-inherited-in-xcodes-search-path-settings)
>
> I'm looking for a documentation, too. But I made the experience, that `$(inherited)` can be used to inherit build settings from the project level to the target level. When you define library or header search paths at the project level you can use `$(inherited)` in the target build settings to use these search paths in the search paths of the project targets.

`$(inherited)`可用于将构建设置从项目级别继承到目标级别。



![image-20200916135737065](../../assets/image-20200916135737065.png)

> You can see the flow of `inherited` from right to left
>
> ```
> Resolved <- Target <- xcconfig <- Project <- iOS Default
> ```
>
> So in `inherited` in Target means that Target inherits settings from xcconfig and Project



## Framework Search Paths

>[Framework Programming Guide](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPFrameworks/Tasks/IncludingFrameworks.html)
>
>## Locating Frameworks in Non-Standard Directories
>
>If your project links to frameworks that are not included in any of the standard locations, you must explicitly specify the location of that framework before Xcode can locate its header files. To specify the location of such a framework, add the directory containing the framework to the “Framework Search Paths” option of your Xcode project. Xcode passes this list of directories to the compiler and linker, which both use the list to search for the framework resources.
>
>
>
>**Note:** The standard locations for frameworks are the `/System/Library/Frameworks` directory and the `/Library/Frameworks` directory on the local system.

文档指出，如果你引用的 `Frameworks` 没有在标准位置（`standard locations`），那么，你需要在工程的配置文件里设置` “Framework Search Paths”`， 用来为编译器（`compiler`）和连接器（`linker`）指定搜索路径。



### 如果想让使用ARC机制的代码不使用ARC机制，只需要输入`-fno-objc-arc`



## Xcode 6 遇到的问题

- 去掉了`pch`文件。如果你想有`pch`需要在`building setting`中的`precompile header`设置下路径`$(SRCROOT)/xxx/xxxx.pch`, 然后将`Precompile Prefix Header`为`YES`，预编译后的`pch`文件会被缓存起来，可以提高编译速度。`pch`是预编译文件,我们的一些头文件导入会写到这里如`Foundation.h` 
- 模拟器路径的变更
  - `/Users/username/Library/Developer/CoreSimulator`

- 应用沙盒地址的变更

  - `应用程序文件`、`Document`文件夹、`Library`文件夹、`tmp`文件夹这四个文件放在了不同的目录中。

    - 应用程序文件路径：`/Users/username/Library/Developer/CoreSimulator/Devices/模拟器UDID/data/Containers/Bundle/Application`文件夹下
    - `Document`文件夹、`Library`文件夹、`tmp`文件夹路径：`/Users/username/Library/Developer/CoreSimulator/Devices/模拟器UDID/data/Containers/Data/Application`文件下。这两个路径打开后的文件名，还是经过编码过的，而且，同一个应用中的应用程序文件和`Document`、`Library`、`tmp`文件夹所在的文件夹的文件名是不同的，只能自己找。 

- `NSUserDefault` 文件存储位置变更，`NSUserDefault`是iOS提供的本地化数据存储方式，会在沙盒中自动创建一个`.plist`的配置文件，将数据保存起来，在读取时，以`NSDictionary`字典来进行接收 

  - `/Users/username/Library/Developer/CoreSimulator/Devices/模拟器UDID/data/Library/Preferences`文件夹下

#### Xcode 6.3 新增：Nullability Annotations

这一新特性的核心是两个新的类型注释：**__nullable**和**__nonnull**。从字面上我们可以猜到，**__nullable**表示对象可以是NULL或nil，而**__nonnull**表示对象不应该为空。当我们不遵循这一规则时，编译器就会给出警告。



#### Nonnull 区域设置 (Audited Regions)

如果需要每个属性或每个方法都去指定nonnull和nullable，是一件非常繁琐的事。苹果为了减轻我们的工作量，专门提供了两个宏：

- NS_ASSUME_NONNULL_BEGIN
- NS_ASSUME_NONNULL_END

在这两个宏之间的代码，所有简单指针对象都被假定为nonnull，因此我们只需要去指定那些nullable的指针。



## Xcode 7 遇到的问题

### does not contain bitcode
在`Build Setting `里面搜索`bitcode`,把`Enable Bitcode` 更改位`NO`

#### Bitcode
LLVM 是目前苹果采用的编译器工具链，Bitcode 是 LLVM 编译器的**中间代码**的一种编码LLVM的前端可以理解为C/C++/OC/Swift等编程语言，LLVM的后端可以理解为各个芯片平台上的汇编指令或者可执行机器指令数据，那么，BitCode就是位于这两者直接的中间码。 

LLVM的编译工作原理是前端负责把项目程序源代码翻译成 Bitcode 中间码，然后再根据不同目标机器芯片平台转换为相应的汇编指令以及翻译为机器码。这样设计就可以让LLVM成为了一个编译器架构，可以轻而易举的在LLVM架构之上发明新的语言(前端)，以及在 LLVM 架构下面支持新的CPU(后端)指令输出，虽然Bitcode仅仅只是一个中间码不能在任何平台上运行，但是它可以转化为任何被支持的CPU架构，包括现在还没被发明的CPU架构，也就是说现在打开Bitcode功能提交一个App到应用商店，以后如果苹果新出了一款手机并CPU也是全新设计的，在苹果后台服务器一样可以从这个App的 Bitcode 开始编译转化为新CPU上的可执行程序，可供新手机用户下载运行这个App。

上传到服务器的bitcode给苹果带来更好处是: 
以后新设计了新指令集的新CPU，可以继续从这份bitcode开始编译出新CPU上执行的可执行文件，以供用户下载安装。

但是bitcode给开发者带来的不便之处就是: 
没用bitcode之前，当应用程序奔溃后，开发者可以根据获取的的奔溃日志再配上上传到苹果服务器的二进制文件的调试符号表信息DSYM可以还原程序运行过程到奔溃时后调用栈信息，对问题进行定位排查。但是用了bitcode之后，用户安装的二进制不是开发者这边生成的，而是苹果服务器经过优化后生成的，其对应的调试符号信息丢失了，也就无法进行前面说的还原奔溃现场找原因了。



### 网络请求失败：`App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure. Temporary exceptions can be configured via your app's Info.plist file.`

原因：`iOS9`默认使用`HTTPS`请求。解决：暂时退回`http`请求：在工程的`Info.plist`文件里添加`NSAppTransportSecurity`字典类型的，添加一个元素：`key`为`NSAllowsArbitraryLoads`，值为`YES`

  ```json
  <key>NSAppTransportSecurity</key>
  <dict>
  	<key>NSAllowsArbitraryLoads</key>
  	<true/>
  </dict>
  ```

### `iOS 9` 使用 `URL scheme`必须将其加入**白名单**

  - `canOpenURL: failed for URL: "mqqopensdkapiV2://qqapp" - error: "This app is not allowed to query for schememqqopensdkapiV2”`
  - 解决方法：`Info.plist`文件中添加一个`key`为`LSApplicationQueriesSchemes`的数组值，里面包含需要添加白名单的`string`类型的` scheme`。在项目中使用了qq，微信等分享登录功能，需要添加对应的key

- 系统`framework`问题，升级之后`framework`全变红了（并不影响程序运行），替换成`9.0`版本的`framework`即可，旧版本的` .dylib`后缀库文件在`Xcode7`中已经换成` .tbd`了，相应进行替换即可



### XCode 9

xcode 9 带来了不少的变化，其中，构建系统选项出现了 **New Build System (Preview) **  选项

![image-20200928161248046](../../assets/image-20200928161248046.png)





## XCode 10 遇到的问题

> ld: library not found for -lstdc++.6.0.9
> clang: error: linker command failed with exit code 1 (use -v to see invocation)
> linker command failed with exit code 1 (use -v to see invocation)

**解决：**

把以下3个文件copy到 
`/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/usr/lib` 目录

- libstdc++.6.0.9.tbd
- libstdc++.6.tbd
- libstdc++.tbd




## Xcode更新后,无法跳转和查看注释

本来默认有跳转功能,但是更新后一直都是`Symbol not found`,原因是工程的索引文件在更新后被破坏了,必须到`~/Library/Developer/Xcode/DerivedData`下把相应的工程的文件夹给删除就行了.删除之后再`rebuild`一次,就可以看到索引了.`Xcode`经常出一些莫名其妙的问题。



## 让使用ARC机制的代码不使用ARC机制

**导航文件--> 选中`Targets`--> 选择 `Build Phases` --> 展开`Compile Sources`**

**Compiler Flags**，只需要输入 `-fno-objc-arc`



## Xcode 常用快捷键

| 按键                         | 描述                               |
| ---------------------------- | ---------------------------------- |
| **Command + Shift + F**      | 跳转到搜索导航器【**非常实用**】   |
| **Command + Shift + j**      | **显示当前文件，在导航拦中的位置** |
| Command+Control+ 上下箭头    | **切换头文件和源文件**             |
| Command + g                  | 在当前文件中查找下一个搜索到的字符 |
| Option+Command+[             | **向上移动该行（可多行）**         |
| Option+Command+]             | **向下移动该行**                   |
| Command + [                  | 左移代码块                         |
| Command + ]                  | 右移代码块                         |
| command + ctrl + 左右方向键  | 返回刚才的文件                     |
| Option + command +左右方向键 | 折叠或展开代码块                   |
| Command + ctrl + o           | 跳转到官方文档                     |
| Command + ctrl + j           | 跳转到变量的定义文件               |
| Ctrl+k                       | 删除本行                           |
| Ctrl+d                       | 删除光标右边的字符                 |
| Ctrl+t                       | 交换光标左右两边的字符             |
| Command + .                  | 结束运行                           |
| Command + 左箭头/右箭头      | 模拟器横竖屏切换                   |
| Command + 鼠标单击           | 跳转到定义                         |
| Alt +   鼠标放在模拟器上     | 可以模拟多点触摸                   |



## could not find developer disk image

**解决**
将你对应缺少的 Disk Image 版本放入
> /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport

![](../../assets/2020-xcode-1.png)



### Copy items if needed

![image-20200928160435050](../../assets/image-20200928160435050.png)

如图，在xcode会产生**蓝色文件夹（folder），其一般作为资源文件夹使用，与黄色文件夹的主要区别是不参与编译**，所以说如果你在这些文件夹下编写的逻辑代码是不参与编译的，其他文件也不能直接引用它们，若引用其中文件需要全路径。



![image-20200928160620278](../../assets/image-20200928160620278.png)

如图，在xcode下**黄色文件夹（group）是逻辑文件夹**，主要是为了逻辑上的分组，如果手动创建（通过New Group选项）group并不会真正创建一个文件夹文件，该文件夹下的文件则会散乱的存放在工程根目录下。当然我们通常会让Xcode中的文件树与实际工程文件中的文件树保持一致。

上述，我们都勾选了 *Copy items if needed*，它会自动复制一份相同的文件到你的工程中，引用的是复制后在工程目录中的位置。若不勾选，文件的引用位置则是文件的原位置（不建议这样做，如果该文件在工程外被删除，工程则无法引用，所以还是复制一份到工程中，这样更利于工程文件的管理）。



### Distribution requires enrollment in Apple Developer Program

解决：更新证书



### The file “XXX” couldn’t be opened because you don’t have permission to view it.

![image-20200928162904934](../../assets/image-20200928162904934.png)



解决：直接点击Xcode -> Preferences ->找到DerivedData删除即可


### Xcode 里 `-ObjC`  
在 Xcode 的 `Build Settings` ->  `Other Linker Flags` 里面加入`-ObjC` 的作用：它的作用就是将静态库中所有的和对象相关的文件都加载进来。

解释：
之所以使用该标志，和Objective-C的一个重要特性：类别（category)有关。
Unix的标准静态库实现和Objective-C的动态特性之间有一些冲突：**Objective-C 没有为每个函数（或者方法）定义链接符号，它只为每个类创建链接符号**。**这样当在一个静态库中使用类别来扩展已有类的时候，链接器不知道如何把类原有的方法和类别中的方法整合起来**，就会导致你调用类别中的方法时，出现`selector not recognized` 错误。

### 使用 `-ObjC` 后
1. `undefined symbols`，说明工程中没有引入第三方引用的库。
2. `duplicate symbols`，说明第三方库中的类名和工程中的类名，或其他第三方库中的类名**重名**了。

规避解决：
1. 避免对系统类加 `category` 这样，别人用到你的库时，不加 `-ObjC` 参数也可以用你的库
2. 如果库中用到了其它的第三方的源代码，尤其是用的比较普遍的，如 `Reachability`，一定要对这些类重命名，最常见的做法是给类加个**前缀**，以避免别人用你的库时，产生 `duplicate symbols` 的问题。

> There are 2 other possibilities for this error besides duplicate files
>
> 1. You may importing `.m` file instead of `.h` by mistake
> 2. Constants kXXX already defined in some other files. As you are defining those constants in constant file then just import that file in `Prefix.pch` file and remove from everywhere else.



<img src="../../assets/image-20211209154534927.png" alt="image-20211209154534927" style="zoom:80%;" />



#### `-all_load`  `-force_load`
本来这样就可以解决问题了，不过在64位的Mac系统或者iOS系统下，链接器有一个 bug，会导致只包含有类别的静态库无法使用 `-ObjC` 标志来加载文件。变通方法是使用 `-all_load` 或者 `-force_load` 标志，它们的作用都是加载静态库中所有文件，即使没有objc代码。
- `all_load` 作用于所有的库
- `-force_load` 后面必须要指定具体的文件。eg. `-force_load $(PROJECT_DIR)/yourframewokname.framework/yourframeworkname`

### The target … overrides the `OTHER_LDFLAGS` build setting defined in `Pods/Pods.xcconfig

> This definitely works most of the time:
>
> Go to your target Build Settings -> Other linker flags -> double click . Add `$(inherited)` to a new line.
>
> If you have problem with "...target overrides the GCC_PREPROCESSOR_DEFINITIONS build setting defined in..." then you must add $(inherited) to your target Build Settings -> Preprocessor Macros

<img src="../../assets/image-20210202143423954.png" alt="image-20210202143423954" style="zoom:50%;" />





### 曾经的一次上传 ipa

![image-20211202111810715](../../assets/image-20211202111810715.png)

使用了高版本SDK API 会报错

<img src="../../assets/image-20211202112513490.png" alt="image-20211202112513490" style="zoom:80%;" />

<img src="../../assets/image-20211202112542517.png" alt="image-20211202112542517" style="zoom:80%;" />

<img src="../../assets/image-20211202112633818.png" alt="image-20211202112633818" style="zoom:80%;" />

<img src="../../assets/image-20211202112608811.png" alt="image-20211202112608811" style="zoom:80%;" />

<img src="../../assets/image-20211202112709116.png" alt="image-20211202112709116" style="zoom:80%;" />

<img src="../../assets/image-20211202112811580.png" alt="image-20211202112811580" style="zoom:80%;" />

<img src="../../assets/image-20211202112749471.png" alt="image-20211202112749471" style="zoom:80%;" />

<img src="../../assets/image-20211202112209482.png" alt="image-20211202112209482" style="zoom:80%;" />

<img src="../../assets/image-20211202112841798.png" alt="image-20211202112841798" style="zoom:80%;" />

<img src="../../assets/image-20211202112923781.png" alt="image-20211202112923781" style="zoom:80%;" />

<img src="../../assets/image-20211202112419770.png" alt="image-20211202112419770" style="zoom:80%;" />





## 参考文献

- [Xcode操作流](https://blog.csdn.net/phunxm/article/details/17044337)

- [Xcode6 模拟器路径【修改】](https://blog.csdn.net/simon699/article/details/39398453)
- [升级Xcode7后遇到的一些问题](https://blog.csdn.net/he15023306643/article/details/48684229)
- [关于bitcode, 知道这些就够了]( http://xelz.info/blog/2018/11/24/all-you-need-to-know-about-bitcode/)