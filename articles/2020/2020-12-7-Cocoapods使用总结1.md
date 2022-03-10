## Cocoapods 使用总结（一）

 <a href="./articles/2016-8-27-Cocoapods搭建OC私repo.md">Cocoapods搭建OC私repo</a>
 <a href="./articles/2016-4-3-cocoaPods的简单使用.md">CocoaPods的简单使用</a>

以往记录的两篇笔记，但是技术这东西，是在不断迭代的，所以尽管是同一个技术就需要不断的学习。

### 背景

写这篇文章的起因是，创建的pod模块，在swift文件中无法直接 `import` ，然后在`podfile` 中，在引入模块的时候添加了如下设置：

```ruby
pod 'xxxModule', :git => 'http://xxx.git', :branch => 'dev/1.0.1', :modular_headers => true
```

相信你一下子就留意到了 `:modular_headers` 。最终的结果也是，我可以在 `.swift` 中直接 `import xxxModule`，于是就有了这篇文章。



### 给项目安装依赖 -- Pod

#### Build configurations

```ruby
pod 'XXXModule', :configurations => ['Debug', 'Dev']
```

这样，`XXXModule` 就只能在构建配置列表中启用。



#### `:git` 、 `:branch`

拿文章开头那段 podfile 里边的配置来说，项目将添加依赖为 `http://xxx.git`  仓库下的 `dev/1.0.1` 分支。



#### 可以指定tag  `:tag` 或者 `:commit`

```ruby
pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :tag => '0.7.0'

# Or 

pod 'AFNetworking', :git => 'https://github.com/gowalla/AFNetworking.git', :commit => '082f8319af'
```



#### 可以指定本地路径 `:path`

```ruby
 pod 'xxxModule', :path => '../'
```



#### Subspecs

场景：

有一个 `xxxModule` 

- x App 需要其中的，m1 和 m3 功能
- y App 需要其中的 m2 功能
- z App 需要其中的 m2 和 m3 功能

这种情况下，如果 m1 m2 m3 完全独立，我们可以把 `xxxModule` 拆成 3个 独立的仓库。但是，Cocoapods 给我们提供了一个更加优秀的方式给使用方提供相关源码，而不是全部源码。各个 Subspec 之间也可以有依赖关系，依赖其他第三方库等。

```ruby
# case 1
pod 'xxxModule', :subspecs => ['m1', 'm3']
# case 2
pod 'xxxModule/m2'
# case 3
pod 'xxxModule', :subspecs => ['m2', 'm3']
```



##### 拓展：podspec 代码该如何实现 subspec ？

### Examples:

> Subspecs with different source files.

```ruby
subspec 'Twitter' do |sp|
  sp.source_files = 'Classes/Twitter'
end

subspec 'Pinboard' do |sp|
  sp.source_files = 'Classes/Pinboard'
end
```

> Subspecs referencing dependencies to other subspecs.

```ruby
Pod::Spec.new do |s|
  s.name = 'RestKit'

  s.subspec 'Core' do |cs|
    cs.dependency 'RestKit/ObjectMapping'
    cs.dependency 'RestKit/Network'
    cs.dependency 'RestKit/CoreData'
  end

  s.subspec 'ObjectMapping' do |os|
  end
end
```

> Nested subspecs.

```ruby
Pod::Spec.new do |s|
  s.name = 'Root'

  s.subspec 'Level_1' do |sp|
    sp.subspec 'Level_2' do |ssp|
    end
  end
end
```



---

### 2022-3-10 M1 执行 pod install --verbose 报错

**2022 Update**

If you can use Homebrew to manage cocoapods.

```dart
# Uninstall the local cocoapods gem
sudo gem uninstall cocoapods

# Reinstall cocoapods via Homebrew
brew install cocoapods
```

------

**2021 Solution**

```dart
#1 Install ffi
sudo arch -x86_64 gem install ffi

#2 Re-install dependencies
arch -x86_64 pod install
```

 

In addition to running terminal in Rosetta:

1. Right-click on Terminal in Finder
2. Get Info
3. Open with Rosetta

I installed a gem that seems to be related to the symbol not found in the error:

```dart
sudo gem install ffi
```

After doing this, cocoapods runs as expected.
