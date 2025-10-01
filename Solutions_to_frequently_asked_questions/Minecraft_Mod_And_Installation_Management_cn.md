# Minecraft Mod And Installation Management

(本文适用于 Minecraft Java 版,官方启动器)

Minecraft Java 版有着庞大的 mod 生态,为了更好的游玩 Minecraft Java 版,我们需要管理其 mod,和 Installation (游戏实例)

## 怎么加 mod 启动器?

加载 mod 需要 mod 启动器,比较知名的有 faric 和 forge

先去 faric 或 forge 的官网下载它们(应该是一个 Java 程序),然后双击运行依照提示安装即可

## 怎么加 mod?

(这里以 faric 1.20.1 为例)

1. 为了方便与稳定,请找一个位置创建一个 `Minecraft` 目录,并在此目录下创建一个 `faric_1.20.1` 目录 (假设路径为 `~/Documents/Minecraft/faric_1.20.1`)

   (安装 faric 后, faric 可能会在启动器中自动给你新建一个 Installation,这个可以删掉,下文以新建配置为例)

2. 打开你的 Minecraft 官方启动器,点击 Java 版,点击顶部的 Installation (配置) 选项进入其中

3. 点击新建一个配置,输入此配置名字,接着在此配置的目录存放路径中填写 `~/Documents/Minecraft/faric_1.20.1` (或选择),然后点击保存

   在保存旁边有一个自动启动(或安装)按钮,如果不选手动启动配置即可

4. 启动 Minecraft,但启动后什么都不要干,直接退出

5. 进入 `faric_1.20.1` 目录,你会发现新建的配置已经在 `faric_1.20.1` 目录中初始化完成

6. 打开 `faric_1.20.1` 目录中的 `mod` 目录,放入你想要的添加的 mod,请注意如果你用的是 fabric,你还需要额外下载对应版本的 `fabric_api.jar`,并放入`mod` 目录中,forge 则不需要

在向 `mod` 目录中添加 mod 时请注意:

* Minecraft 版本(这个不匹配,基本不能用)

* mod 启动器版本(这个看情况,相差一些可能不会有多大问题)

在添加完 mod 后,再次启动 MC 即可游玩你添加的 mod

Minecraft mod 网站中比较知名的是 [https://www.curseforge.com/minecraft](https://www.curseforge.com/minecraft) 和 [https://modrinth.com/mods](https://modrinth.com/mods)

至此你已经可以安装 mod 了

## 报错

如果在添加完 mod 后启动 Minecraft 时报错或直接崩溃,请查看日志后进行修复, fabic 和 forge 也有可能会有弹窗提醒你

有一些 mod 可能会对 Java 版本有要求,如果你需要快速切换或管理你的各 Java 版本,可以使用 [jenv](/Solutions_to_frequently_asked_questions/jenv_cn.md)

## curseforge

curseforge 是一个庞大的 mod 网站(当然不只是 Minecraft mod),如果你觉得手动处理 mod 中的冲突,依赖和更新太麻烦,可以下载 curseforge 的应用程序来帮你快速方便的管理 mod
