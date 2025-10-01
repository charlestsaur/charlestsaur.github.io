# lima linux installation

(本文适用于 macOS, ChatGPT 为共同作者)

lima [https://github.com/lima-vm/lima](https://github.com/lima-vm/lima) 是一个轻量级虚拟化解决方案,允许在 macOS 上原生运行 Linux 虚拟机.它使用 macOS 的 Virtualization.framework(vz) 或 QEMU 来实现虚拟化,适用于开发,测试,容器运行等场景

具体请看官方 README

**用 lima 的理由是它足够轻量**

## Table of Contents

- [lima linux installation](#lima-linux-installation)
  - [Table of Contents](#table-of-contents)
  - [Install lima](#install-lima)
  - [Deploy Linux (Ubuntu)](#deploy-linux-ubuntu)
  - [Update the virtual machine](#update-the-virtual-machine)
  - [cloud-init](#cloud-init)
  - [Change the virtual machine directory](#change-the-virtual-machine-directory)
  - [Virtual machine hard disk location](#virtual-machine-hard-disk-location)
  - [vz](#vz)
  - [Using ISO files](#using-iso-files)
  - [Linux desktop](#linux-desktop)
  - [Report an error](#report-an-error)
  - [View Architecture](#view-architecture)
  - [Unix domain socket](#unix-domain-socket)

## Install lima

这里使用 Homebrew 安装:

```zsh
brew install lima
```

lima 会创建一个 `~/.lima` 目录

## Deploy Linux (Ubuntu)

这里我们以 vz 驱动 (macOS Virtualization.framework)举例, lima 默认就是 vz, vz 具体内容请看后文

在安装好 lima 后,我们开始部署 Ubuntu server:

(直接 start 就行,确认提示后它会自动拉取镜像)

```zsh
limactl start --name=my_ubuntu template://ubuntu
```

这时你应该会看到一个提示,确认无误后回车

在 `limactl start ...` 执行完后,你的 `~/.lima` 目录树状结构应该会变成这样:

```plaintext
.
├── _config/                         # 全局配置 (有时会出现)
└── my_ubuntu/                       # 你的虚拟机目录
    ├── arch                         # 架构信息 (例如 x86_64, aarch64)
    ├── console.log                  # 虚拟机的控制台输出日志
    ├── lima.yaml                    # 启动时使用的配置文件(合并后的)
    ├── user-data                    # cloud-init 的 user-data (初始化脚本)
    ├── network.yaml                 # 网络配置(可能存在)
    ├── serial.sock                  # 串口通信的 Unix socket
    ├── ssh.config                   # SSH 配置 (可以直接 SSH 连接)
    ├── ssh.sock                     # SSH 代理 socket
    ├── vm.sock                      # lima 的控制 socket
    ├── diffdisk/                    # VZ 后端时的挂载目录,包含映射的文件系统变更
    └── mounts/                      # 你挂载的 host 目录的映射配置和 socket
```

然后进入已启动的虚拟机:

```zsh
limactl shell my_ubuntu
```

`pwd`, `ls` 一下,可以发现 lima 直接将本机的 home 目录挂载到虚拟机的 `/Users` 目录下了

⚠️ 它只是 **只读** home 目录,不能改动

## Update the virtual machine

若想更新镜像,可执行 `limactl prune` 清除缓存,或更改 `lima.yaml` 的镜像地址(`lima.yaml` 配置文件具体请看后文)

## cloud-init

lima 使用 cloud-init 来初始化虚拟机,包括设置用户名,SSH 密钥,网络配置等.你在 `lima.yaml` 文件中指定的初始化配置最终会被生成成一个 ISO 文件 `cidata.iso`,由虚拟机挂载并自动读取

## Change the virtual machine directory

如果想让虚拟机里面是一个“独立”的 Linux 文件系统,不挂载任何宿主目录,我们可以更改虚拟机的配置文件

更改之前先停止虚拟机:

```zsh
limactl stop my_ubuntu
```

(在这里如果遇到: `ERRO[0000] [hostagent] dhcp: unhandled message type: RELEASE` 报错,无需担心,具体请看后文)

然后更改 `~/.lima/my_ubuntu/lima.yaml` 文件

未更改的 `lima.yaml` 应该是这样的:

```plaintext
minimumLimaVersion: 1.1.0
images:
# Try to use release-yyyyMMdd image if available. Note that release-yyyyMMdd will be removed after several months.

- location: "https://cloud-images.ubuntu.com/releases/plucky/release-20250701/ubuntu-25.04-server-cloudimg-amd64.img"
  arch: "x86_64"
  digest: "sha256:92ad8714338b1d3be605b7981137a7c356b23527ff1aba8e80cf44bc3aa2f8da"
- location: "https://cloud-images.ubuntu.com/releases/plucky/release-20250701/ubuntu-25.04-server-cloudimg-arm64.img"
  arch: "aarch64"
  digest: "sha256:26d0ac2236f12954923eb35ddfee8fa9fff3eab6111ba84786b98ab3b972c6d8"
- location: "https://cloud-images.ubuntu.com/releases/plucky/release-20250701/ubuntu-25.04-server-cloudimg-riscv64.img"
  arch: "riscv64"
  digest: "sha256:66f420cb992e3076207ce933b7f96550d27b05ead561b7a5e48d281d01b151de"
- location: "https://cloud-images.ubuntu.com/releases/plucky/release-20250701/ubuntu-25.04-server-cloudimg-armhf.img"
  arch: "armv7l"
  digest: "sha256:f37d82f55df80bdb09bbeb403f84e6921b8e4435610fed22ffec436af2e4dd6d"
- location: "https://cloud-images.ubuntu.com/releases/plucky/release-20250701/ubuntu-25.04-server-cloudimg-s390x.img"
  arch: "s390x"
  digest: "sha256:1d03fedf3aea85438ce70d745edf611b80c6f88a049bb44167c24ae2ec9d4fda"
- location: "https://cloud-images.ubuntu.com/releases/plucky/release-20250701/ubuntu-25.04-server-cloudimg-ppc64el.img"
  arch: "ppc64le"
  digest: "sha256:5922f8ea7958f15af5a5d8a4efec38d66af31c0a853fba5fb0790ec25562b53a"
# Fallback to the latest release image.
# Hint: run `limactl prune` to invalidate the cache

- location: https://cloud-images.ubuntu.com/releases/plucky/release/ubuntu-25.04-server-cloudimg-amd64.img
  arch: x86_64

- location: https://cloud-images.ubuntu.com/releases/plucky/release/ubuntu-25.04-server-cloudimg-arm64.img
  arch: aarch64

- location: https://cloud-images.ubuntu.com/releases/plucky/release/ubuntu-25.04-server-cloudimg-riscv64.img
  arch: riscv64

- location: https://cloud-images.ubuntu.com/releases/plucky/release/ubuntu-25.04-server-cloudimg-armhf.img
  arch: armv7l

- location: https://cloud-images.ubuntu.com/releases/plucky/release/ubuntu-25.04-server-cloudimg-s390x.img
  arch: s390x

- location: https://cloud-images.ubuntu.com/releases/plucky/release/ubuntu-25.04-server-cloudimg-ppc64el.img
  arch: ppc64le

mounts:

- location: "~"

- location: "{{.GlobalTempDir}}/lima"
  mountPoint: /tmp/lima
  writable: true
# # NOTE: Intel Mac with macOS prior to 15.5 requires setting vmType to qemu
# # https://github.com/lima-vm/lima/issues/3334
# vmType: qemu
```

我们只需要将 `- location: "~"` 注释掉就好了

如果你想要将宿主机的其他目录到虚拟机中,更改 `- location: "~"` 其中指定的路径即可

lima 还支持 sshfs 和 9p 方式的文件共享,具体配置可以看官方文档

在 `mounts:` 下还有一段配置:

```plaintext
- location: "{{.GlobalTempDir}}/lima"
  mountPoint: /tmp/lima
  writable: true
```

该挂载是 lima 默认模板中的临时目录,主要用于 cloud-init 与 containerd 等组件的通信或初始化配置

详细一点来说,这是挂载你宿主机的一个临时目录 (一般在 `/tmp` 或 `/var/folders/...`) 到 lima 虚拟机内部的 `/tmp/lima`,用于 lima 运行时内部通信 (比如提供初始配置数据),不是用户日常使用的共享目录

通常:

* 它只在启动过程中用到

* 启动后你可以几乎忽略它

* 一些 lima 版本如果没有这个挂载,cloud-init 和容器代理（containerd, nerdctl）可能会出错

如果你只是想运行一个独立的 Ubuntu 虚拟机 (无 nerdctl,无 cloud-init),你可以注释掉这段,通常能正常启动, lima 不强制要求这一段

但是注意:

* 如果你之后使用 lima 来运行 nerdctl / docker / containerd 相关功能,这段挂载可能是必须的

* 如果系统报错说找不到某些初始化配置文件时,也可能是因为你注释了它

⚠️ 如果你看到报错类似:

* `No such file or directory: /tmp/lima/cloud-init.iso`

* `nerdctl: unable to connect to containerd socket`

* `mount: permission denied on tmpfs`

那就说明你不能注释掉这段,得恢复它

修改完配置文件后我们再重新启动虚拟机:

```zsh
limactl start my_ubuntu
```

此时, ~/.lima 目录的结构大致如下:

```plaintext
.
├── _config
│   ├── networks.yaml
│   ├── user
│   └── user.pub
└── my_ubuntu
    ├── basedisk
    ├── cidata.iso
    ├── cloud-config.yaml
    ├── default_ep.sock
    ├── default_fd.sock
    ├── diffdisk
    ├── ha.sock
    ├── ha.stderr.log
    ├── ha.stdout.log
    ├── lima-version
    ├── lima.yaml
    ├── serialv.log
    ├── ssh.config
    ├── vz-efi
    └── vz-identifier
```

虚拟机 my_ubuntu 目录下的东西,具体解释为:

| 文件/目录名                       | 说明                                                                |
| ---------------------------- | ----------------------------------------------------------------- |
| `basedisk` / `diffdisk`      | 用于 overlay 的磁盘镜像数据 (vz 驱动下管理虚拟机存储的方式)                              |
| `cidata.iso`                 | cloud-init 配置生成的 ISO,用于初始设置用户名,密码, SSH 密钥等                         |
| `cloud-config.yaml`          | cloud-init 的原始配置                                                  |
| `ha.sock`, `ha.stdout.log` 等 | hostagent 的 socket 与日志                                            |
| `serialv.log`                | 虚拟串口输出日志（类似串口终端）                                                  |
| `ssh.config`                 | lima 自动生成的 SSH 配置（你可以 `ssh -F ~/.ssh/config lima-my_ubuntu` 用它登录） |
| `vz-*` 开头的文件                 | `vz` 驱动的标识信息,如 EFI 引导,标识符等                                        |
| `lima.yaml`                  | 你的虚拟机配置文件                                                          |

但这时我们发现没有 `disk.qcow2`, `alpine.img`, `ubuntu.img` 这样的文件 (QEMU 驱动下,会产生的虚拟机的硬盘镜像)

这是因为你使用的是 vz 驱动 (macOS Virtualization.framework),不是 QEMU 驱动

* QEMU 驱动下, lima 会下载 `.qcow2` 镜像,并以硬盘形式存在 (如 `disk.qcow2`)

* vz 驱动不使用 `.qcow2`,它直接调用 macOS 的底层虚拟化接口 (更干净快速),磁盘结构以其他方式管理,不暴露出传统的 `.img` 文件

## Virtual machine hard disk location

(lima) 在 vz 模式下,虚拟磁盘不以 `.img` 或 `.qcow2` 的形式暴露出来,而是拆分为内部结构如下:

| 文件/目录名                    | 作用                                  |
| ------------------------- | ----------------------------------- |
| `basedisk`                | 系统镜像的基础层,通常是只读的 (比如 Ubuntu)          |
| `diffdisk`                | **你的所有更改都在这里**,包括安装的软件,下载的文件,创建的目录等 |
| `cidata.iso`              | cloud-init 设置,比如你的 SSH key,用户名等     |
| `vz-efi`, `vz-identifier` | EFI 引导信息和虚拟机标识,用于启动                 |

也就是说,你的个人文件,安装软件的数据都存在 `diffdisk` 中

## vz

vz 是 lima 的一种虚拟机驱动,在 macOS 上专门用来创建轻量虚拟机,背后依赖的是 macOS 的虚拟化框架

vz 相比于 QEMU,它的性能更加好,硬件加速更好 (因为 vz 原生支持 macOS),但 vz 的限制也多一些 (e.g. 网络, USB),而 QEMU 更灵活,硬件模拟能力强

如果你在启动时看到类似:

```plaintext
INFO[0005] Starting the instance "my_ubuntu" with VM driver "vz"
```

就说明你用的是 vz 驱动.默认配置模板 `template://ubuntu` 就会启用 vz（除非你手动设定为 QEMU）

如果你想你可以这样启动:

```zsh
limactl start --name=qemu_vm --set vmType=qemu template://ubuntu
```

这样 lima 会用 QEMU 启动 VM,并下载 `.qcow2` 镜像到 `~/.lima/qemu_vm` 下

## Using ISO files

如果你有 Ubuntu 的 `.iso` 镜像,你也可以用 ISO 镜像启动 lima 虚拟机

但是我们不推荐你使用自己下载的 ISO 镜像启动 lima 虚拟机:

| 问题               | 原因                                        |
| ---------------- | ----------------------------------------- |
| ❌ 不支持自动安装        | ISO 不是 cloud image, lima 的 cloud-init 配置没用 |
| ❌ 没有 SSH         | ISO 安装前没有 SSH 服务, lima 的自动 SSH 登录失效        |
| ❌ 手动安装麻烦         | 每次重启还得选择安装,或你要自己创建持久磁盘、引导配置               |
| ✅ Cloud Image 优点 | 启动快、有 cloud-init、自带 SSH、公钥登录、最适合 lima     |

如果你要用 ISO 镜像启动 lima 虚拟机的话,请在 `lima.yaml` 中指定这个 ISO:

(假设你的 ISO 在 `/Users/yourname/Downloads/ubuntu.iso`)

```plaintext
arch: "aarch64"  # 或 "x86_64",看你的 ISO 是哪种架构
images:
  - location: "/Users/yourname/Downloads/ubuntu.iso"
    arch: "aarch64"
    digest: ""  # 不验证 sha256
```

但这样只是挂载 ISO 镜像为光驱启动,它不会自动安装,系统启动后会进入 Ubuntu 安装器界面 (就像你在实体机装系统一样),你必须一步步安装

## Linux desktop

虽然我们使用 lima 就是为了轻量快速,但 lima 也可以启动 Ubuntu desktop

如果你想用 lima 启动 Ubuntu desktop 你要先安装 lima 的 Ubuntu desktop 版本的虚拟机

然后在你的 Ubuntu desktop 版本的虚拟机中的 `lima.yaml` 文件的最上面写入:

```plaintext
vmType: "vz"
gui: true
```

当然你也可以用一个 server 版本的 Ubuntu 在修改配置文件后再 `sudo apt install ubuntu-desktop`,安装一个图形化桌面

若你启动的是 ISO 镜像且想修改引导方式,可在启动时快速按 <kbd>Esc</kbd> 或 <kbd>F2</kbd> 进入 UEFI 界面

如果你是用 ISO 镜像来启动 Ubuntu desktop, 同理

## Report an error

⚠️ 非致命警告

* ERRO[0000] [hostagent] dhcp: unhandled message type: RELEASE

这是 DHCP 发来的释放 (RELEASE)类型消息, hostagent 没有处理它

这类信息常见于虚拟网络接口关闭时,对 lima 的实际运行没有影响,可以忽略

## View Architecture

查看我们的 lima 虚拟机架构,可以先运行虚拟机,然后进入虚拟机,使用:

```zsh
uname -m
```

看输出就知道了

## Unix domain socket

“共享套接字” 是指将一个 Unix 域套接字 (Unix domain socket) 挂载 (bind-mount) 到 lima 虚拟机中,使得虚拟机内的程序可以通过这个套接字访问宿主机的服务,反之亦然

它本质上是通过文件系统中的套接字路径完成跨虚拟机与宿主机进程之间通信的桥梁

举个例子: Docker 的共享套接字

```plaintext
containerd:
  system: false
  user: false
```

这意味着 lima 不会内置启动 containerd,它就需要通过共享套接字连接宿主机 Docker (或者你手动启用的 containerd)

你就会在 YAML 里看到 lima 把宿主机的 Docker 套接字映射进 VM 里:

```plaintext
mounts:
  - location: "/var/run/docker.sock"
    writable: false
```

在虚拟机里,这个文件就像在宿主机上一样存在,运行容器工具就能用它来控制 Docker 了

反过来也可以: lima VM 内暴露套接字给宿主机

比如你在 lima 里跑了个 containerd,想让 host macOS 使用它,也可以这么挂:

```plaintext
mounts:
  - location: "/Users/yourname/.lima/sockets/containerd.sock"
    mountPoint: "/run/containerd.sock"
    writable: true
```

若你在 lima 虚拟机中使用 nerdctl,并希望控制宿主机的 Docker,可映射:

```plaintext
mounts:
  - location: "/var/run/docker.sock"
    mountPoint: "/var/run/docker.sock"
    writable: false
```

然后在 VM 中使用:

```bash
nerdctl ps
```

为什么用套接字,而不是 TCP?

* 安全性更高: Unix domain socket 是文件,不容易暴露在公网或本地网络上

* 权限管理方便: 可以通过文件权限控制访问

* 性能更好: 比 TCP/IP 少了协议栈的开销

* 和 Docker/containerd 默认行为一致: 它们本来就默认监听 Unix 套接字
