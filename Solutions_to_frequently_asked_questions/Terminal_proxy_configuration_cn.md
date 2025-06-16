# Terminal proxy configuration

[< Index >](/index.md)

---

本文所说的操作是适用于 Macos 15.4.1 的,所说的命令是适用于 zsh (其实如果你用的是 bash 也没多大问题)

## 确认系统代理设置

打开 系统设置 > VPN。

确保你已经配置了所需的代理，并记下代理服务器的地址和端口(认证代理需要用户名和密码)

(想要看到服务器地址 , 端口 , 用户名 , 密码 这些信息请点击 系统设置 > VPN 中你想要配置在 Terminal 中配置的那个代理后面的ⓘ

1. 服务器地址在 Proxies 中的 Server

2. 端口在 Proxies 中的 Port

3. 用户名在 L2TP over IPSec 中的 Account name

4. 密码在 L2TP over IPSec 中的 Password

* 因为后面的操作用的格式是 ```用户名:密码``` ,所以你需要保证你的 User authentication 设置的是 Password,但如果你用的是 RSA SecurID , Certificate , Kerberos , CryptoCard 的话也可以用我后面说的方法试一下,就把 RSA SecurID , Certificate , Kerberos , CryptoCard 当 Password 就行了,但我不保证一定能成功,因为我也没用过 RSA SecurID , Certificate , Kerberos , CryptoCard

)

## 实验

### 1. 配置(临时,非永久)

非认证代理:

```zsh

export http_proxy=http://代理服务器地址:端口
export https_proxy=http://代理服务器地址:端口
export all_proxy=http://代理服务器地址:端口

```

认证代理:

```zsh

export http_proxy=http://用户名:密码@代理服务器地址:端口
export https_proxy=http://用户名:密码@代理服务器地址:端口
export all_proxy=http://用户名:密码@代理服务器地址:端口

```

### 2. 测试

输入:

```zsh

curl https://www.google.com

```

如果输出 ```<!doctype html> ... </script>      </body></html>%``` 这么一大坨,即代表成功

如果成功就可以永久配置了

## 永久配置

### 1. 现将配置写入 .zshrc 配置文件中

```zsh

echo "export http_proxy=http://用户名:密码@代理服务器地址:端口" >> ~/.zshrc
echo "export https_proxy=http://用户名:密码@代理服务器地址:端口" >> ~/.zshrc
echo "export all_proxy=http://用户名:密码@代理服务器地址:端口" >> ~/.zshrc

```

(非认证代理就按上文的写就行)

或着用 nano , vim 直接编辑 .zshrc (文件地址为 ~/.zshrc )

### 2.保存你的编辑

如果是用 ```echo "..." >> ~/.zshrc``` 方式写入的需要用以下命令保存

(如果是用 nano , vim 编辑的,请按照 nano , vim 的方式保存,当然也建议再用以下的命令保存一遍,因为 nano , vim 中的保存可能只是将 nano , vim 编辑器中编辑的东西保存了,有可能 .zshrc 的更改没有被保存)

```zsh

source ~/.zshrc

```

建议在用 ```source ~/.zshrc``` 保存后再将 Terminal 重启一遍

### 3. 测试

跟上面的非永久配置的测试一样

输入

```zsh

curl https://www.google.com

```

如果输出 ```<!doctype html> ... </script>      </body></html>%``` 这么一大坨,即代表成功

## 注意事项

### 1. SOCKS 代理

如果你的代理是 SOCKS 类型，环境变量需要使用 socks5:// 或 socks4://，例如：

```zsh

export http_proxy=socks5://代理服务器地址:端口
export https_proxy=socks5://代理服务器地址:端口

```

### 2. 排除某些地址

如果你想要某些地址不走代理，可以设置 no_proxy：

```zsh

export no_proxy="localhost,127.0.0.1,*.local"

```

### 3. 认证代理

我要再说一遍

如果代理需要用户名和密码，格式为：

```zsh

export http_proxy=http://用户名:密码@代理服务器地址:端口
export https_proxy=http://用户名:密码@代理服务器地址:端口

```

### 4. 验证代理

这个我也要再说一遍

使用以下命令确认代理是否生效：

```zsh

env | grep -i proxy
curl -I https://www.google.com

```

为什么要加 ```env | grep -i proxy``` 它有什么用?

```env | grep -i proxy``` + ```curl -I https://www.google.com``` 的作用:

1. ```env | grep -i proxy``` ：

列出当前 ```shell``` 环境中所有包含 ```proxy```（不区分大小写）的环境变量，例如 ```http_proxy``` , ```https_proxy``` , ```all_proxy``` 等

用来确认是否正确设置了代理环境变量，以及它们的具体值是否符合预期（例如地址和端口是否正确）

示例输出(假设代理为 ```http://192.168.1.100:8080```)：

```zsh

http_proxy=http://192.168.1.100:8080
https_proxy=http://192.168.1.100:8080
all_proxy=http://192.168.1.100:8080

```

2. ```curl -I https://www.google.com``` ：

使用 ```curl``` 发送一个 HTTP HEAD 请求到 https://www.google.com，只返回响应头

如果代理设置正确，你会看到类似 ```HTTP/2 200``` 的响应，表明通过代理成功访问了目标网站

如果代理未生效或有问题，可能会报错（如 ```Failed to connect``` 或 ```Connection refused```）

* ```curl``` 默认行为是发送一个 ```GET``` 请求 并输出 响应体（body），也就是网页的内容或接口返回的内容

* ```curl -I``` 发送一个 ```HEAD``` 请求，只获取响应头（headers），不包括响应体。这在你只想查看服务器响应状态、类型、缓存信息等时很有用

### 5. 有关 ```all_proxy```

如果遇到某些工具（如 git、pip ）无法识别 ```all_proxy```，可以额外设置 ```http_proxy``` 和 ```https_proxy``` 作为后备

如果你的代理是 SOCKS 类型或有复杂需求（如绕过特定地址），建议参考之前的详细回答，使用 ```proxychains``` 或设置 ```no_proxy```

[< Index >](/index.md)