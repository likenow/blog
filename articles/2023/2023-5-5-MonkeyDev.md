## 逆向开发

### 工具

#### 手机越狱

https://canijailbreak.com/

https://checkra.in/

http://wall.supplies/

https://unc0ver.dev/

> 注意手机设备是否满足越狱的条件：处理器/ 操作系统版本等



##### 越狱手机软件安装

- Cydia
  - OpenSSH
  - Substrate
  - PreferenceLoader



#### 砸壳

https://github.com/frida/frida

>如果出现：`frida ios dump timeout was reached` 可以从这几个方面排查：
>
>- 目标App 是否在运行中，如果运行中，需要先杀死该App进程
>- 连接到iOS设备的USB线可能已断开，或者设备可能已经重启。
>-  Frida服务可能已经停止运行，或者没有在设备上正常安装。 设备上的应用程序可能已经崩溃或被终止。 
>
>建议检查连接的设备，确保Frida服务已经正常安装并运行，并检查应用程序的运行状态。如果问题仍然存在，可以尝试使用 `frida-ps -U` 命令查看当前正在运行的进程，并使用 `frida-trace -U -i "process_name" `命令跟踪进程中的函数调用，以找出可能导致问题的原因。
>
>



### 开发包

[Monkey Dev](https://github.com/AloneMonkey/MonkeyDev/wiki/%E5%AE%89%E8%A3%85) 💪

[libimobiledevice](https://github.com/libimobiledevice)

[Theos](https://theos.dev/docs/installation)

​	[Logos](https://theos.dev/docs/logos-syntax)

[PreferenceLoader](https://iphonedev.wiki/index.php/PreferenceLoader)



### 获取 Bundle ID

####  1 解压IPA文件 

把`.ipa`文件的后缀重命名为`.zip` 解压`.zip`压缩包，会有一个Payload 文件夹。点开Payload文件夹会有一个`.app`的文件，右键点击`.app`文件 -> Show Package content， 将会打开新的文件夹包含很多文件.。用Xcode打开文件`info.plist`，就可以找到`bundle id`。这个前提是得有一个 ipa 文件。



#### 2 用Mac自带工具Console 查看bundle id

已经装在iphone里面的 app 用数据线连接手机和Mac。打开Mac 应用Console. 在左侧栏Devices中，选择你的设备 运行你要查看的APP。 在搜索栏里面输入App的名字。你可以看到在process列中找到SpringBoard， Message列中找到 Bootstrapping，点击这一行， bundleId 类似于com.xxxx.xxx 。 但是如果名字没有出现，找起来比较费劲，因为好多系统日志。



#### 3 在APP下载链接里查看 打开Mac的浏览器

搜索app在App Store中iTunes的链接。 比如: https://itunes.apple.com/cn/app/wei/id414478124

拷贝在URL中id后面的数字

打开浏览器非美国区在https://itunes.apple.com后面加上地区，比如新加坡 https://itunes.apple.com/sg/xxx?id=123456789

当提示下载text file文本文件，保存文件 `.txt`文件，文件中搜索 bundleId 即可。这个方法比较实用！推荐



### 推荐

推荐论坛： https://iosre.com/ 

推荐文章：https://juejin.cn/post/6942419453879517192