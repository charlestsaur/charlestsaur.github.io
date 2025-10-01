# Starship Configuration Recommendations

(本文档适用于 macOS 系统，使用的 shell 为 zsh)

macOS 自带的 Terminal 界面不太好用. 例如,没有文件路径显示,所有颜色都一样难以区分,以及没有方便的 Git 状态显示

我们可以使用 starship 来优化我们的 Terminal

你无需过于担心 starship 的安全性,因为它是一个大型开源项目,[https://github.com/starship/starship](https://github.com/starship/starship)

请按照其 GitHub 上的说明安装 starship

如果你想使用 starship 并且已经安装,我推荐以下配置(其配置文件通常为 `starship.toml`):

(请注意,如果你想使用以下配置,请确保终端使用 MesloLGS NF 系列字体,可参考终端字体设置 [Terminal Font Settings](Terminal_font_settings.md))

```toml
# Basic Settings
add_newline = false
format = """
$directory\
$git_branch\
$git_status\
$nodejs\
$rust\
$python\
$docker_context\
$package\
$cmd_duration\
$jobs\
$character
"""

# Table of contents
[directory]
truncation_length = 0
truncate_to_repo = false
style = "cyan"
read_only = " 󰌾"

# Git branches
[git_branch]
symbol = " "
style = "purple"

# Git Status
[git_status]
style = "yellow"
format = '([\[$all_status$ahead_behind\]]($style) )'
conflicted = "⚔️ "
ahead = "⇡${count}"
behind = "⇣${count}"
diverged = "⇕⇡${ahead_count}⇣${behind_count}"
untracked = "[?](blue)"
stashed = "📦 "
modified = "📝 "
staged = "➕ "
renamed = "🌀 "
deleted = "🗑️ "

# Node.js
[nodejs]
symbol = " "
format = "[$symbol($version )]($style)"
style = "green"

# Rust
[rust]
symbol = "[R]🛠️  "
format = "[$symbol($version )]($style)"
style = "red"

# Python
[python]
symbol = "🐍 "
format = "[$symbol${version}($virtualenv)]($style) "
style = "yellow"

# Docker
[docker_context]
symbol = "🐳 "
style = "blue bold"
format = "[$symbol$context]($style) "

# Package information (npm, cargo, etc.)
[package]
symbol = "📦 "
format = "[$symbol$version]($style) "
style = "208"

# Execution time
[cmd_duration]
min_time = 2000
format = "⏱ [$duration]($style) "
style = "bold yellow"

# Number of background tasks
[jobs]
symbol = "✦"
style = "blue"

# Command Prompt Style
[character]
success_symbol = "[❯](bold green)"
error_symbol = "[✗](bold red)"
```
