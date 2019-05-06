# SSH (Secure SHell) 介绍

## 
SSH 是 Secure Shell 的缩写，意为安全外壳协议。它是位于应用层的一种加密的网络传输协议（通过对传输数据进行加密来保证security），用于为远程登录会话和其他网络服务提供安全的通信环境。虽然任何网络服务都可以通过SSH实现安全传输，但最常见的用途是远程登录系统，人们通常利用SSH来传输命令行界面和远程执行命令（[SSH - 维基百科](https://zh.wikipedia.org/zh-hans/Secure_Shell)）。

SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。OpenSSH就是SSH协议的一个著名开源实现，应用非常广泛，很多操作系统和产品（详细名单见[这里](https://www.openssh.com/users.html)）都使用了该软件，因此学会使用OpenSSH工具套件很重要。

OpenSSH 的实现中，包含了如下几类工具：
* 远程操作：ssh, scp, sftp
* 密钥管理：ssh-add, ssh-keysign, ssh-keyscan, ssh-keygen
* 服务端相关：sshd, sftp-server, ssh-agent

ssh命令
ssh命令是OpenSSH套件中的客户端连接工具，用于安全地连接远程主机。其用法如下：
```
     ssh [-1246AaCfgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec] [-D  [bind_address:]port] [-e escape_char]
         [-F configfile] [-i identity_file] [-L  [bind_address:]port:host:hostport] [-l login_name] [-m mac_spec]
         [-O ctl_cmd] [-o option] [-p port] [-R  [bind_address:]port:host:hostport] [-S ctl_path]
         [-w local_tun[:remote_tun]] [user@]hostname [command]
```
各选项的具体含义，可以参看man文档。这里介绍下基本的用法。
以haycheng的身份登录主机hostname：
```
ssh haycheng@hostname
```
或通过IP地址登录：
```
ssh haycheng@3.14.159.26
```


**参考资料：**
* [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
* [OpenSSH](https://www.openssh.com/)

