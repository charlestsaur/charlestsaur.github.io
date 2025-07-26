# jenv

(本文档适用于 macOS, ChatGPT为共同作者)

Jenv([https://github.com/jenv/jenv](https://github.com/jenv/jenv)) 是一款 java 管理工具,可以帮我们方便的管理 java 版本

📌 jenv 是怎么工作的?

它只是通过修改当前 shell 的 `JAVA_HOME` 环境变量来“选择”你想用的 Java

它并不会卸载,覆盖或修改任何 Java 安装

你添加 Java 版本时,它只是记录路径,不复制文件

你切换版本时,它只是改变 `JAVA_HOME`,类似:

```zsh
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-17.jdk/Contents/Home
```

它不会改 /usr/bin/java 或 /Library 任何文件，所以非常安全、可控。

## Installing jenv

1. 先安装项目本身

   (Homebrew, MacPorts 直接安装就行,它会帮你配置好配置文件)

   源码安装:

   ```zsh
   git clone https://github.com/jenv/jenv.git ~/.jenv
   ```

   (这里是在 home 目录下克隆,如果你不想,更改即可)

2. 根据你用的 shell,执行对应命令添加 jenv 到 `PATH` 并初始化

   (这里是以在 home 目录下克隆仓库为例,如果不是,改一下路径即可)

   bash 用户，执行：

     ```zsh
     echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
     echo 'eval "$(jenv init -)"' >> ~/.bash_profile
     source ~/.bash_profile
     ```

   zsh 用户，执行：

   ```zsh
   echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
   echo 'eval "$(jenv init -)"' >> ~/.zshrc
   source ~/.zshrc
   ```

1. 启用 export 插件（使 JAVA_HOME 能自动切换）

   ```zsh
   eval "$(jenv init -)"
   jenv enable-plugin export
   ```

   (↑ 具体可以查看 Replenish)

   然后重新启动终端或执行:

   ```zsh
   exec $SHELL -l
   ```

2. 准备 Java

   看你自己想怎么装,有就不装

3. 将 Java 添加到 jenv 管理

   查看你装的 java 版本:
   
   ```zsh
   /usr/libexec/java_home -V
   ```

   然后添加到 jenv

   ```zsh
   jenv add "$(/usr/libexec/java_home)"
   ```

   你可以用下面命令确认添加成功:

   ```zsh
   jenv versions
   ```

4. 设置默认 Java 版本

   设置全局默认版本:

   ```zsh
   jenv global 21.0.2
   ```

   (你也可以不设,继续用系统设置的版本)

   设置某个项目目录的本地版本:

   ```zsh
   cd /path/to/project
   jenv local 21.0.2
   ```

   只在当前 shell 会话设置版本:

   ```zsh
   jenv shell 21.0.2
   ```

5. 验证 JAVA_HOME 是否生效

   ```zsh
   echo $JAVA_HOME
   ```

   它应该指向类似 ~/.jenv/versions/21.0.2 的目录

-***额外提示***-

安装完毕后运行：

```zsh
jenv doctor
```

可以检查 jenv 是否正常工作

如果用 fish shell,配置方式稍有不同,具体可查 jenv 官方文档


## Replenish

### eval

`eval` 是 Linux/macOS shell 里的一个命令,作用是执行参数里的字符串内容作为命令

举个简单例子:

```zsh
eval "echo hello"
```

相当于直接执行:

```zsh
echo hello
```

在你说的这句:

```zsh
eval "$(jenv init -)"
```

它的意思是:

先执行 `jenv init -` 这个命令,把它的输出结果(通常是一串 shell 命令,比如设置环境变量,定义函数等)拿出来

再用 `eval` 执行这串输出的命令,从而完成 jenv 的初始化配置

为什么要这样做?

因为 `jenv init -` 输出的其实是要写到 shell 配置里的命令,比如:

```zsh
export PATH="$HOME/.jenv/bin:$PATH"
export some_other_variable=...
# 还有定义一些函数等
```

通过 `eval "$(jenv init -)"`,你不用手动复制粘贴, shell 会自动执行它输出的所有初始化步骤

简单总结:

`eval`:把参数当成代码执行

`jenv init -`:输出初始化 jenv 所需的 shell 命令

`eval "$(jenv init -)"`:执行 jenv 初始化的命令，让 jenv 生效

你如果想更直观看,先试试:

```zsh
jenv init -
```

你会看到它输出了一堆 shell 命令,执行 `eval "$(jenv init -)"` 就是让 shell 运行这些命令

### export

`jenv enable-plugin export` 是用来开启 jenv 的一个插件功能,叫做 `export` 插件

它的作用是什么?

* 默认情况下, jenv 只帮你切换 Java 版本,但不会自动修改环境变量 `JAVA_HOME`

* 而很多程序都依赖环境变量 `JAVA_HOME` 来找到正确的 Java 路径

export 插件做了什么?

* 它会自动帮你设置和更新 `JAVA_HOME` 环境变量,让你的终端和应用都能用到你当前选中的 Java 版本

* 开启后，你切换 Java 版本(比如用 `jenv global 17`), `JAVA_HOME` 也会自动跟着变

举个简单例子:

没启用 export 插件之前:

```zsh
jenv global 17
echo $JAVA_HOME
# 可能是空的，或者旧版本路径
```

启用后:

```zsh
jenv enable-plugin export
jenv global 17
echo $JAVA_HOME
# 显示 /Users/yourname/.jenv/versions/17 这样的路径
```
