# Terminal Font Settings

[< Index >](/index.md)

---

(This document applies to macOS 15)

The default font of Terminal on macOS 15 should be Menlo. This font looks good, but if you need to display Unicode private area characters (Private Use Area, PUA) in your terminal, then this font is not easy to use, because it will be displayed as several horizontal bars or garbled characters

If you want to display Unicode private area characters (Private Use Area, PUA), I recommend using the MesloLGS NF series of fonts

| Project | Original version of MesloLGS (IBM source) | Nerd Font patch version MesloLGS NF |
| ------------- | --------------------------------------- | --------------------------------------------------------- |
| Font file name | `MesloLGS-Regular.ttf` | `MesloLGS NF Regular.ttf` |
| Font internal name (name) | `MesloLGS` | `MesloLGS NF` |
| Support icon characters (PUA) | ❌ Not supported | ✅ Supported | |
| Font source | [IBM original font](https://github.com/IBM/plex) | [Nerd Fonts official website](https://www.nerdfonts.com/) |

After downloading the font, you can first install it to the font library of macOS, and then select this font:

Terminal > Settings > Profiles > Font

You may encounter terminal encoding problems. In rare cases, incorrect terminal character encoding settings will also cause icon display abnormalities. Make sure you are using UTF-8:

Terminal > Settings > Profiles > Advanced > International > Text encoding

[< Index >](/index.md)