---
title: iOS集成ijkplayer播放视频
date: 2017-08-05 23:40:01
tags:
- 视频
- iOS
categories:
- iOS
---

## 集成 ijkplayer 遇到的问题

其实，在[ijkplayer](https://github.com/Bilibili/ijkplayer)项目上已经有一个说明文档。在这里记录一下自己通过`framework`的方式集成遇到的问题。

- 需要先编译 `ffmpeg`

- 在打`framework`包的时候，需要分别在真机和模拟器上打包，然后通过脚本命令合并起来。但是再发布版本里记住只需要真机包即可。

```shell
lipo -create "真机版本路径" "模拟器版本路径" -output "合并后的文件路径"
```

<!--more-->

## ijkplayer 实现一个简单的直播视频

- ijkplayer 用法比较简单，其实只要有直播地址，就能直播

### 拉流 URL 来观看直播

```objective-c
#pragma mark - 播放直播
- (void)playLive {
    
    // 拉流 URL
    NSURL *liveURL = [NSURL URLWithString:_live.stream_addr];
    
    IJKFFMoviePlayerController *playerVC = [[IJKFFMoviePlayerController alloc] initWithContentURL:liveURL withOptions:nil];
    
    [self.view insertSubview:playerVC.view belowSubview:_backBtn];
    
    [playerVC.view mas_makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(self.view);
    }];
    
    // 记录成员变量
    _playerVC = playerVC;
    
    [playerVC prepareToPlay];
    
    [playerVC play];
}
```

### 离开页面需要停止播放

```objective-c
#pragma mark - 当离开当前直播间的时候，要停止播放
- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    
    // 界面消失，停止播放
    [_playerVC pause];
    [_playerVC stop];
    [_playerVC shutdown];
}
```



## 参考文献

- [ijkplayer](https://github.com/Bilibili/ijkplayer)
- [iOS端集成ijkplayer实现直播#拉流#播放](https://www.jianshu.com/p/e4e4f7f3767d)