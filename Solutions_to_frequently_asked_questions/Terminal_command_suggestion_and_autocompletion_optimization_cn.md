# Terminal Command Suggestion And Autocompletion Optimization

[< Index >](../index.md)

---

(本文档使用的操作系统是 macOS 并使用 Homebrew)

## 推荐配置与插件

首先我们先打开 `zsh` 自带的补全,在 `~/.zshrc` 中写入:

```plaintext
autoload -Uz compinit
compinit
```

然后可以让其立即生效:

```zsh
source ~/.zshrc
```

(但还是建议重启一遍终端)

然后我们可以使用 **zsh-syntax-highlighting** 这个插件

它可以检测当前输入的命令是否存在,存在就变绿,不存在就变红,请它的检测是是实时的,即你不运行只是在输入它也会检测,看个人习惯,有人可能会认为不好用

首先安装:

```zsh
brew install zsh-syntax-highlighting
```

安装好后在 `~/.zshrc` 中写入:

```plaintext
# zsh-syntax-highlighting
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

一定要将这一行写在你的 `.zshrc` 的最后面,不然可能会报错,不生效

然后使用 `source` (但也还是建议再重启一遍)

这个时候它就会实时检测当前输入的命令是否存在了

但由于它会将存在的命令变绿,这和我的终端配色配合起来有点不好看,而且我也希望它只将不存在的变红,存在的就不变色,所以我在还加入了一段配置,这是整个 **zsh-syntax-highlighting** 插件在我的 `.zshrc` 中的配置:

```plaintext
# zsh-syntax-highlighting
typeset -A ZSH_HIGHLIGHT_STYLES
ZSH_HIGHLIGHT_STYLES[command]='none'
ZSH_HIGHLIGHT_STYLES[builtin]='none'
ZSH_HIGHLIGHT_STYLES[unknown-token]='fg=red'
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

(记得写完用 `source` ,也还是建议再重启一遍)

具体可以解释为:

`typeset -A ZSH_HIGHLIGHT_STYLES` -- 声明一个关联数组 ZSH_HIGHLIGHT_STYLES，用于配置命令高亮样式

`ZSH_HIGHLIGHT_STYLES[command]='none'` -- 对外部命令（如 ls、git）不进行高亮

`ZSH_HIGHLIGHT_STYLES[builtin]='none'` -- 对内建命令（如 cd、echo）也不进行高亮

`ZSH_HIGHLIGHT_STYLES[unknown-token]='fg=red'` -- 对无法识别的命令（拼写错误或不存在的命令）使用红色字体提示

`source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh` -- 加载 zsh-syntax-highlighting 插件主脚本，使上述配置生效


## 不建议插件

### zsh-autosuggestions

它会根据你上一次输入这个命令的历史来提供补全建议的,这对于像 `cd` 和 `git commit` 这种命令很不好,因为它将你上一次的用这个命令的时候加的参数显示出来了,比如(假设):

* 打出 `cd` 后,它后面会用灰色显示 `&nbsp;~/Documents/other/001`,但你大概率是不会再进入上次进入的目录

* 打出 `git commit -m` 后,它会在后面用灰色显示 `&nbsp;"updated the .gitignore"`,但你几乎不会再用和上次一样的提示信息

### **zsh-autocomplete**

它会在你打出一个命令后列出所有可用的选项

(它可以用用 ↑ 和 ↓ 选中,选中一行按下 Enter,就会补全,但不会运行,如果你的选中了当前显示的最后一行再按 ↓ ,如果还有未显示的话它就会选中并显示未显示的下一行)

但这样会导致看起来很不舒服,尤其是终端窗口开的小的时候,它可能会突然将你正在输入的那一行突然抬高,并且你一般是用不到的,常用的都记得到,不常用的用 `zsh` 自带的补全效果一样,或更好,比如:

另外如果你使用的是 Homebrew 下载的话,还需要注意在删除 **zsh-autocomplete** 时,用:

```zsh
brew rm zsh-autocomplete # or brew uninstall
```

有可能会删不干净,可以加上 `--zap` ,这会尝试删除 `~/Library` 下的相关文件

[< Index >](../index.md)
