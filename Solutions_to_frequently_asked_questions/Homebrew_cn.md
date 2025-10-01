# Homebrew

(本文章与 AI 协同完成)

## Install Homebrew

以下是 Homebrew 的官方网站和维基百科对其的记录

[https://brew.sh/](https://brew.sh/)

[https://github.com/Homebrew/brew](https://github.com/Homebrew/brew)

[https://en.wikipedia.org/wiki/Homebrew_(package_manager)](https://en.wikipedia.org/wiki/Homebrew_(package_manager))

### 1. Check if Homebrew is installed

运行以下命令：

```zsh
which brew
```

如果输出为空，说明 Homebrew 未安装，需要安装。

### 2. Install Homebrew

如果你还没安装 Homebrew，可以执行以下命令：

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装过程中可能会提示你输入密码，输入后按 Enter 继续。

### 3. 添加 Homebrew 到 PATH

安装完成后，Homebrew 可能没有自动添加到 PATH，你需要手动配置：

对于 Apple Silicon（M1/M2/M3）：

```zsh
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/opt/homebrew/bin/brew shellenv)"
```

对于 Intel Mac：

```zsh
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/usr/local/bin/brew shellenv)"
```

然后执行：

```zsh
source ~/.zshrc
```

检查是否生效：

```zsh
brew --version
```

如果能正确输出版本号，说明 Homebrew 已成功配置。

### 🔹 Homebrew 还能安装 GUI 应用

Homebrew 还能安装 macOS 图形界面应用（比如 Google Chrome、VS Code）：

```zsh
brew install --cask google-chrome
brew install --cask visual-studio-code
```

这样就不用去官网下载 .dmg 手动拖动安装了。

### 🔸 想快速完成一次完整的升级+清理流程，可以直接运行：

```zsh
brew update && brew upgrade && brew autoremove && brew cleanup
```

具体信息可以阅读 ↓

## Update

### Update Homebrew

为了安全与更好的使用体验可以定期更新 Homebrew

如果你想要更新 Homebrew,可以运行:

```zsh
brew update
```

### Update Software

除了更新 Homebrew 本身以外,你可能还需要更新已安装的软件:

先检查哪些软件有新版本:

```zsh
brew outdated
```

然后更新所有软件:

```zsh
brew upgrade
```

(当然你也可以只更新某个软件，例如: ```brew upgrade visual-studio-code```)

在使用更新 Homebrew 或软件后,可以运行:

```zsh
brew cleanup
```

它会清理旧版本软件和无用缓存

## uninstall

### Removing Software

假如你要删除软件 001,可以运行:

```zsh
brew uninstall 001
brew remove 001
brew rm 001
```

(上面三条命令等价，任选其一即可)

### Removing dependencies

软件有可能会带有依赖项,如果你要删除所有无用依赖项,可以运行:

```zsh
brew autoremove
```

这条命令的逻辑是

它会删除不再被“你主动安装的软件”所依赖的软件包，这些通常是：

* 不是你直接 brew install 安装的

* 现在也没有被你装的任何软件依赖

哪怕这些包本身是一个功能完整、可以独立运行的程序，如果它只是曾经被作为依赖安装，现在又没人用它了，那它就会被 autoremove 清理掉

可以理解为“只要是对现有的我直接用 brew install 安装的软件无用的东西，它都会删掉”

如果你想在删除之前先预览即将删除的内容,可以运行:

```zsh
brew autoremove --dry-run
```

在使用 autoremove 删除无用依赖后,可以运行:

```zsh
brew cleanup
```

它会清理旧版本软件和无用缓存

如果你想查看你主动装的软件,可以运行:

```zsh
brew leaves
```

它会列出所有顶级软件(也就是你真正用 `brew install` 装的)

### Removing Configuration Files

除了依赖项,有的软件还有可能会有配置文件

在删除时加上 `--zap` Homebrew 就会尝试删除 `~/Library` 下的相关文件,比如:

```zsh
brew uninstall --zap visual-studio-code
```

❗️请注意: `brew uninstall` 可能无法删除所有配置文件,所以请先使用 `brew info` 查看其官网地址调查其配置文件存放地址及存放逻辑
