# Terminal proxy configuration

[< Index >](../index.md)

---

The operations described in this article are applicable to Macos 15.4.1, and the commands described are applicable to zsh (actually, if you use bash, it is not a big problem)

## Confirm system proxy settings

Open System Settings > VPN.

Make sure you have configured the required proxy and note the proxy server address and port (authentication proxy requires username and password)

(To see the server address, port, username, password information, please click ⓘ behind the proxy you want to configure in Terminal in System Settings > VPN

1. Server address in Proxies

2. Port in Proxies

3. User name in L2TP over IPSec

4. Password in L2TP over IPSec

* Because the format used in the following operations is ```user name: password```, you need to make sure your User authentication is set to Password, but if you are using RSA SecurID, Certificate, Kerberos, CryptoCard, you can also try the method I will describe later, just use RSA SecurID, Certificate, Kerberos, CryptoCard as Password, but I can't guarantee that it will succeed, because I have never used RSA SecurID, Certificate , Kerberos , CryptoCard.

)

## Experiment

### 1. Configuration (temporary, non-permanent)

Non-authentication proxy:

```zsh

export http_proxy=http://proxy server address:port
export https_proxy=http://proxy server address:port
export all_proxy=http://proxy server address:port

```

Authentication proxy:

```zsh

export http_proxy=http://username:password@proxy server address:port
export https_proxy=http://username:password@proxy server address:port
export all_proxy=http://username:password@proxy server address:port

```

### 2. Test

Input

```zsh

curl https://www.google.com

```

If the output is ```<!doctype html> ... </script> </body></html>%``` Such a large lump means success.

If successful, you can configure it permanently.

## Permanent configuration

### 1. Now write the configuration to the .zshrc configuration file

```zsh

echo "export http_proxy=http://username:password@proxy server address:port" >> ~/.zshrc

echo "export https_proxy=http://username:password@proxy server address:port" >> ~/.zshrc

echo "export all_proxy=http://username:password@proxy server address:port" >> ~/.zshrc

```

(For non-authenticated proxy, just write as above)

Or use nano, vim to edit .zshrc directly (file address is ~/.zshrc ).

### 2. Save your edits

If you write it with ```echo "..." >> ~/.zshrc```, you need to save it with the following command.

(If you use nano, vim, please save it in the same way as nano or vim. Of course, it is also recommended to save it again with the following command, because saving in nano or vim may only save what is edited in nano or vim editor, and it is possible that the changes in .zshrc are not saved).

```zsh

source ~/.zshrc

```

It is recommended to restart Terminal after saving with ```source ~/.zshrc```.

### 3. Test

Same as the non-permanent configuration test above.

Input:

```zsh

curl https://www.google.com

```

If the output is ```<!doctype html> ... </script> </body></html>%``` such a big lump, it means success.

## Notes

### 1. SOCKS proxy

If your proxy is SOCKS type, the environment variable needs to use socks5:// or socks4://, for example:

```zsh

export http_proxy=socks5://proxy server address:port

export https_proxy=socks5://proxy server address:port

```

### 2. Exclude certain addresses

If you want certain addresses not to go through the proxy, you can set no_proxy:

```zsh

export no_proxy="localhost,127.0.0.1,*.local"

```

### 3. Authentication proxy

I'll say it again.

If the proxy requires a username and password, the format is:

```zsh

export http_proxy=http://username:password@proxy server address:port

export https_proxy=http://username:password@proxy server address:port

```

### 4. Verify the proxy

I'll say it again.

Use the following command to confirm whether the proxy is effective:

```zsh

env | grep -i proxy
curl -I https://www.google.com

```

Why add ```env | grep -i proxy``` What is its use?

```env | grep -i proxy``` + ```curl -I https://www.google.com``` Function:

1. ```env | grep -i proxy``` ：

List all environment variables containing ```proxy``` (case insensitive) in the current ```shell``` environment, such as ```http_proxy``` , ```https_proxy``` , ```all_proxy``` etc.

Used to confirm whether the proxy environment variables are set correctly and whether their specific values ​​meet expectations (such as whether the address and port are correct).

Sample output (assuming the proxy is ```http://192.168.1.100:8080```)：

```zsh

http_proxy=http://192.168.1.100:8080
https_proxy=http://192.168.1.100:8080
all_proxy=http://192.168.1.100:8080

```

2. ```curl -I https://www.google.com``` ：

Use ```curl``` to send an HTTP HEAD request to https://www.google.com and return only the response headers.

If the proxy is set up correctly, you will see a response similar to ```HTTP/2 200```, indicating that the target website was successfully accessed through the proxy.

If the proxy is not effective or has problems, you may get an error (such as ```Failed to connect``` or ```Connection refused```).

* The default behavior of ```curl``` is to send a ```GET``` request And output the response body, that is, the content of the web page or the content returned by the interface.

* ```curl -I``` sends a ```HEAD``` request to get only the response headers, not the response body. This is useful when you only want to view the server response status, type, cache information, etc.

### 5. About ```all_proxy```

If you encounter some tools (such as git, pip) that cannot recognize ```all_proxy```, you can set ```http_proxy``` and ```https_proxy``` as a fallback.

If your proxy is SOCKS type or has complex requirements (such as bypassing specific addresses), it is recommended to refer to the previous detailed answer and use ```proxychains``` or set ```no_proxy```.

[< Index >](../index.md)
