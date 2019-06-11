# SSH (Secure SHell) 介绍

## 
SSH 是 Secure Shell 的缩写，意为安全外壳协议。它是位于应用层的一种加密的网络传输协议（通过对传输数据进行加密来保证security），用于为远程登录和其他网络服务提供安全的通信环境。虽然任何网络服务都可以通过SSH实现安全传输，但最常见的用途是远程登录系统，人们通常利用SSH来传输命令行界面和远程执行命令（[SSH - 维基百科](https://zh.wikipedia.org/zh-hans/Secure_Shell)）。

SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。OpenSSH就是SSH协议的一个著名开源实现，应用非常广泛，很多操作系统和产品（详细名单见[这里](https://www.openssh.com/users.html)）都使用了该软件，因此学会使用OpenSSH工具套件很重要。

OpenSSH 的实现中，包含了如下几类工具：
* 远程操作：ssh, scp, sftp
* 密钥管理：ssh-add, ssh-keysign, ssh-keyscan, ssh-keygen
* 服务端相关：sshd, sftp-server, ssh-agent


## 参考资料：
1. [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
2. [OpenSSH](https://www.openssh.com/)

