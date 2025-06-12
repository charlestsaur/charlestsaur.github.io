# Homebrew

以下是 Homebrew 的官方网站和维基百科对其的记录

https://brew.sh/

https://github.com/Homebrew/brew

https://en.wikipedia.org/wiki/Homebrew_(package_manager)

## 1. Check if Homebrew is installed

运行以下命令：

```sh
which brew
```

如果输出为空，说明 Homebrew 未安装，需要安装。

## 2. Install Homebrew

如果你还没安装 Homebrew，可以执行以下命令：

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装过程中可能会提示你输入密码，输入后按 Enter 继续。

## 3. 添加 Homebrew 到 PATH

安装完成后，Homebrew 可能没有自动添加到 PATH，你需要手动配置：

对于 Apple Silicon（M1/M2/M3）：

```sh
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/opt/homebrew/bin/brew shellenv)"
```

对于 Intel Mac：

```sh
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/usr/local/bin/brew shellenv)"
```

然后执行：

```sh
source ~/.zshrc
```

检查是否生效：

```sh
brew --version
```

如果能正确输出版本号，说明 Homebrew 已成功配置。

## 4. 安装完成后，你需要让 macOS 识别 brew 命令（只需执行一次）：

```sh
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/opt/homebrew/bin/brew shellenv)"
```

## 🔹 Homebrew 还能安装 GUI 应用

Homebrew 还能安装 macOS 图形界面应用（比如 Google Chrome、VS Code）：

```sh
brew install --cask google-chrome
brew install --cask visual-studio-code
```

这样就不用去官网下载 .dmg 手动拖动安装了。


