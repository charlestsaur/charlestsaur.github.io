# Xcode Update

本文档的更新方法十分简易，仅适用于 Xcode 的轻度使用者

如果你正在使用 Xcode 开发项目建议谨慎对待本文档的操作，更新请查看 [https://developer.apple.com/documentation/updates/xcode](https://developer.apple.com/documentation/updates/xcode)

## Xcode

* 如果你是从 App Store 安装的，那直接去 App Store 点击更新就好

* 如果是从 Apple 开发者网站（[https://developer.apple.com/download/all/?q=Xcode](https://developer.apple.com/download/all/?q=Xcode)）下载的话，那就从 Apple 开发者网页中下载新版，点击新版的安装包，然后在提示重复的窗口中点击替换即可（先将旧的删了也行）

## Command Line Tools

Xcode 更新完后，需要注意 Command Line Tools 是否一起更新了：

```zsh
xcodebuild -version
```

如果报错或版本不对：

```plaintext
❯ xcodebuild -version

xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```

那就重新将系统的开发环境指向它，并初始化一下：

```zsh
sudo xcode-select -s /Applications/Xcode.app
```

具体指：

把“当前使用的开发者工具目录”设置成 `/Applications/Xcode.app`

macOS 有两个地方可能存放开发工具：

`/Library/Developer/CommandLineTools` 👉 只有命令行工具（精简版）

`/Applications/Xcode.app` 👉 完整的 Xcode（含 GUI、Simulator、SDK 等）

默认情况下，你的系统现在用的是“命令行工具路径”，所以很多命令（比如 xcodebuild）找不到完整的功能

执行这条命令就是让系统切换到“完整 Xcode”那边

然后让 Xcode 初始化：

```zsh
xcodebuild -runFirstLaunch
```

包括：

* 同意开发者许可协议

* 安装核心组件

* 注册编译器、SDK

* 让命令行可以正常使用 xcodebuild、clang、swift 等命令

* 如果你从 .xip 文件解压 Xcode.app 而没有打开 GUI 启动过，它的命令行部分就还没“激活”，这条命令会自动完成
