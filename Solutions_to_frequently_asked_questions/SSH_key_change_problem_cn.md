# SSH key change problem

(本文档适用于 macOS, ChatGPT为共同作者)

## 系统提示

用ssh连接机器时,有可能会遇到以下提示

```plaintext
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:jdifh89848nvf87y37huind8yhenu9ffff.
Please contact your system administrator.
Add correct host key in /Users/yourname/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /Users/yourname/.ssh/known_hosts:1
Host key for 192.168.0.101 has changed and you have requested strict checking.
Host key verification failed.
Connection closed
```

这个报错说明你之前连接过 `192.168.0.101` 这台主机,并且它的 SSH 主机密钥(host key)现在变了,和你本地缓存的不一样,系统就会警告你:"可能有人在搞中间人攻击",所以阻止连接

⚠️ 如果你不确定目标机器是否被改动过,请先确认机器安全性,再删除旧 key

如果你 **确认** 是你自己重装或重置了那台机器的系统(或 SSH 服务),这属于正常的密钥变更,你只需要把旧的 key 从 `known_hosts` 中删掉即可

## 解决办法

运行这条命令删除对应的旧密钥:

```zsh
ssh-keygen -R 192.168.0.101
```

然后重新连接：

```zsh
ssh youruser@192.168.0.101
```

这时候系统会提示你新 key 的指纹,并让你确认是否信任,输入 yes 即可

## 原理解释

SSH 为了防止中间人攻击,会在 `~/.ssh/known_hosts` 文件中记录你曾连接过的主机的公钥,如果下次发现该主机的密钥变了,就会报这个错

你的错误信息中写的是:

```plaintext
Offending ECDSA key in /Users/yourname/.ssh/known_hosts:1
```

意思是第 1 行的 key 和当前主机的不一致

你也可以手动编辑这个文件:

```zsh
nano ~/.ssh/known_hosts
```

然后删掉第 1 行,保存退出

如果你不想每次都看到这种验证，也可以在命令行里临时关闭 host key 检查（**不推荐长期使用**）：

```zsh
ssh -o StrictHostKeyChecking=no youruser@192.168.0.101
```
