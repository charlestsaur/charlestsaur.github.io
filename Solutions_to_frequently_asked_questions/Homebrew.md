# Homebrew

[< Index >](../index.md)

---

Here are Homebrew's official website and Wikipedia's record of it

https://brew.sh/

https://github.com/Homebrew/brew

https://en.wikipedia.org/wiki/Homebrew_(package_manager)

## 1. Check if Homebrew is installed

Run the following command:

```zsh
which brew
```

If the output is empty, it means Homebrew is not installed and needs to be installed.

## 2. Install Homebrew

If you haven't installed Homebrew yet, you can execute the following command:

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

During the installation process, you may be prompted to enter a password. Enter it and press Enter to continue.

## 3. Add Homebrew to PATH

After installation, Homebrew may not be automatically added to PATH, you need to configure it manually:

For Apple Silicon (M1/M2/M3):

```zsh
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/opt/homebrew/bin/brew shellenv)"
```

For Intel Mac:

```zsh
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zshrc
eval "$(/usr/local/bin/brew shellenv)"
```

Then execute:

```zsh
source ~/.zshrc
```

Check if it works:

```zsh
brew --version
```

If the version number is displayed correctly, Homebrew has been successfully configured.

## 🔹 Homebrew can also install GUI applications

Homebrew can also install macOS GUI applications (such as Google Chrome, VS Code):

```zsh
brew install --cask google-chrome
brew install --cask visual-studio-code
```

This way you don't have to download .dmg from the official website and manually drag and install it.

[< Index >](/..index.md)
