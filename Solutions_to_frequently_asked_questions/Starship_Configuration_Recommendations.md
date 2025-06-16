# Starship Configuration Recommendations

[< Index >](/index.md)

---

(This document is applicable to macOS system, and the shell used is `zsh`)

The interface of Terminal that comes with macOS is not very easy to use. For example, there is no file path display, all colors are the same and difficult to distinguish, and there is no convenient display of git status

We can use starship to optimize our Terminal

You don’t have to worry too much about the security of starship, because it is a large open source project, [https://github.com/starship/starship](https://github.com/starship/starship)

To install starship, please follow the instructions in its github

If you want to use starship and have installed it, I recommend the following configuration (its configuration file is generally `starship.toml`):

(Please note that if you want to use the following configuration, please make sure to let Terminal use MesloLGS NF series fonts,can refer to [Terminal Font Settings](Terminal_font_settings.md))

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

[< Index >](/index.md)