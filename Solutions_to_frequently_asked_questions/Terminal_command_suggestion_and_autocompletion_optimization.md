# Terminal Command Suggestion And Autocompletion Optimization

[< Index >](/index.md)

---

(This document uses macOS and Homebrew)

## Recommended Configuration and Plugins

First, we turn on the built-in completion of `zsh` and write in `~/.zshrc`:

```plaintext
autoload -Uz compinit
compinit
```

Then you can make it take effect immediately:

```zsh
source ~/.zshrc
```

(But it is still recommended to restart the terminal)

Then we can use the **zsh-syntax-highlighting** plugin.

It can detect whether the currently entered command exists. If it exists, it will turn green, and if it does not exist, it will turn red. Please note that its detection is real-time, that is, it will detect even if you are not running but just typing. It depends on personal habits. Some people may think it is not easy to use.

First install:

```zsh
brew install zsh-syntax-highlighting
```

After installation, in `~/.zshrc` Write in:

```plaintext
# zsh-syntax-highlighting
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

Be sure to write this line at the end of your `.zshrc`, otherwise it may report an error and not take effect.

Then use `source` (but it is still recommended to restart again).

At this time, it will detect whether the currently entered command exists in real time.

But because it will turn the existing command green, it is a bit ugly with my terminal color matching, and I also hope that it will only turn the non-existent ones red, and the existing ones will not change color, so I also added a configuration. This is the configuration of the entire **zsh-syntax-highlighting** plug-in in my `.zshrc`:

```plaintext
# zsh-syntax-highlighting
typeset -A ZSH_HIGHLIGHT_STYLES
ZSH_HIGHLIGHT_STYLES[command]='none'
ZSH_HIGHLIGHT_STYLES[builtin]='none'
ZSH_HIGHLIGHT_STYLES[unknown-token]='fg=red'
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

(Remember to use `source` after writing, and it is still recommended to restart again).

Specifically, it can be explained as follows:

`typeset -A ZSH_HIGHLIGHT_STYLES` -- declare an associative array ZSH_HIGHLIGHT_STYLES, which is used to configure the command highlighting style

`ZSH_HIGHLIGHT_STYLES[command]='none'` -- for external commands (such as ls, git) are not highlighted

`ZSH_HIGHLIGHT_STYLES[builtin]='none'` -- built-in commands (such as cd, echo) are not highlighted

`ZSH_HIGHLIGHT_STYLES[unknown-token]='fg=red'` -- use red font prompts for unrecognized commands (misspelled or non-existent commands)

`source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh` -- load zsh-syntax-highlighting plugin main script to make the above configuration effective

## Plugins are not recommended

### zsh-autosuggestions

It will provide completion suggestions based on the history of the last time you entered this command, which is not good for commands like `cd` and `git commit`, because it displays the parameters you added when you used this command last time, such as (assuming):

* Type `cd` After typing, it will display `&nbsp;~/Documents/other/001` in gray, but you will probably not enter the directory you entered last time.

* After typing `git commit -m`, it will display `&nbsp;"updated the .gitignore"` in gray, but you will almost never use the same prompt message as last time.

### **zsh-autocomplete**

It will list all available options after you type a command.

(It can be selected with ↑ and ↓, and it will complete the line after selecting a line and pressing Enter, but it will not run. If you select the last line currently displayed and press ↓, it will select and display the next line that is not displayed if there is still one).

But this will make it look uncomfortable, especially when the terminal window is small, it may suddenly raise the line you are typing, and you usually don't use it. You can remember the commonly used ones, and the uncommon ones can be completed with the `zsh` built-in completion effect, or better, such as:

Also, if you use If you download it with Homebrew, you also need to pay attention to the use of:

```zsh
brew rm zsh-autocomplete # or brew uninstall
```

It may not be completely deleted, you can add `--zap`, which will try to delete the relevant files under `~/Library`

[< Index >](/index.md)