# Mac 配置开发环境

macOS 系统默认自带ruby、python2、bash等环境，并且因为 macOS 是以 Unix 为基础，所以有传统 Unix/Linux 的特性。在我印象里开发者除了一些基本的开发工具，大家的**开发习惯**、**工具链**没几个是一模一样的，几乎是各有自己的一套**打法**。

但是，当你拿到一台新电脑，如何快速的开始开发工作。换句话说，如何快速的把你习惯的一套打法迁移到新的电脑其实是一个技术活！倒不是觉得一个小时搞定和一天搞定一定要分个对错高下。只是觉得尽快的把一个陌生的“武器”，变成自己趁手的“兵器”很有**成就感**。

对于很多开发者来说，把自己现在熟悉的开发常用配置迁移到新电脑会是首选吧！

## 如何将内容移至新 Mac

苹果有提供“迁移助理”将您的所有文稿、App、用户帐户和设置从一台电脑拷贝到另一台新 Mac 上。

### 迁移助理

怎样传输信息：

- 从 Mac、时间机器备份或者启动磁盘
- 从 Windows PC
- 至另一台电脑

具体可参考 [如何将内容移至新 Mac](https://support.apple.com/zh-cn/HT204350)

这里，有一点需要注意，你从时间机器备份恢复电脑的时候，一定要注意：
> 两台电脑的硬件配置是否一致，比如：你的备份是有 touchbar 的电脑备份，如果你要恢复到一台没有 touchbar 的电脑上，很可能就会恢复失败，必须重置系统！！！别问我怎知道的。。。呜呜呜
> 使用目标磁盘模式在电脑之间移动文件，是需要专门的连接线和转接头的，别瞎整些乱七八糟的线。最好用官方的！！！




### 手动安装和配置

除了一些**极客**，一般免不了要手动搞一搞。ps.  我理解的极客是：**拿到新电脑连上网，执行以下脚本就可以搞定！**

其实自己搞个脚本搞一搞是没问题的，放出来大家用责任太大！所以，**以下工具的具体使用不展开**，安装参考**官方文档！！！**

- Homebrew，包管理

- 使用 defaults 对系统选项的一些基本修改，比如启用三只拖动

  - ```shell
    defaults write com.apple.driver.AppleBluetoothMultitouch.trackpad TrackpadThreeFingerDrag -bool true
    defaults write com.apple.AppleMultitouchTrackpad TrackpadThreeFingerDrag -bool true
    ```

    

- 基于 zsh 的 oh my zsh

  - 常用插件 `plugins=(git osx autojump zsh-autosuggestions zsh-syntax-highlighting)`

    

- 常用软件的安装（顺序无关优先级）

  - Xcode
  - iTerm2，terminal 的替代
  - Hammerspoon，如果会 Lua 如虎添翼
  - Karabiner-Elements，键位绑定
  - OmniPlan
  - QQ
  - Wechat
  - Source Tree
  - SwitchHosts
  - typora，不错的Markdown编辑器
  - vscode，从 sublime text 转过来用vscode
  - wps
  - Charles
  - ogex
  - Dash
  - Shadowsocks
  - Chrome
  - Alfred，业界一致追捧的神兵利器
  - Cocoapods

  


## 总结

就我个人而言，使用了诸如：Hammerspoon、Karabiner-Elements、Dash、Alfred 就再也回不去了。。。使用其他童鞋的电脑明显感觉不舒服，效率低！

目前感觉自己对开发环境的配置远远达不到极客水平，争取写一个安全有效的执行脚本。不过话说回来，谁会天天搞环境呢？哈哈哈


