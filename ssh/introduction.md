# SSH (Secure SHell)

## 背景
传统的网络服务程序，如ftp、pop和telnet等，在网络上是用明文传送口令和数据，别有用心的人就可以截获这些口令和数据，容易受到“中间人”（man-in-the-middle）的攻击。所谓“中间人”的攻击，是指“中间人”冒充真正的服务器接收你传给服务器的数据，然后再冒充你把数据传给真正的服务器。服务器和你之间的数据传送被“中间人”一转手做了手脚之后，就会出现很严重的问题。

因此，需要用一种安全的通信方式，把所有传输的数据进行加密，防止"中间人"这种攻击方式，SSH就是为了实现这种安全的数据传输而出现的。SSH协议最初是在1995年，由芬兰学者Tatu Ylonen设计的，现已成为互联网安全的一个基本解决方案，目前已经成为类Unix系统的标配。几乎所有的类Unix平台，包括Linux、MacOS、Solaris、HP-UX等，都可运行SSH。如果要在Windows系统中使用SSH，需要用到[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)，这是一个在Windows系统中运行SSH、Telnet客户端的免费软件，本文暂不涉及。

## 功能
SSH 是 Secure Shell 的缩写，意为安全外壳协议。它是位于应用层的一种加密的网络传输协议（通过对传输数据进行加密来保证数据安全），其功能点：
- 为远程登录和其他网络服务提供安全的通信环境
- 传输的数据是经过压缩的，可以加快传输的速度

虽然任何网络服务都可以通过SSH实现安全传输，但最常见的用途是远程登录系统，人们通常利用SSH来传输命令行界面和远程执行命令。

## 工具套件
SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。[OpenSSH](https://www.openssh.com/)就是SSH协议的一个著名开源实现，应用非常广泛，很多操作系统和产品（详细名单见[这里](https://www.openssh.com/users.html)）都使用了该软件，因此学会使用OpenSSH工具套件很重要。

OpenSSH 的实现中，包含了如下几类工具：
* 远程操作：ssh, scp, sftp
* 密钥管理：ssh-add, ssh-keysign, ssh-keyscan, ssh-keygen
* 服务端相关：sshd, sftp-server, ssh-agent

## ssh配置
在/etc/ssh/目录下，有ssh_config 和 sshd_config 两个配置文件，分别用于ssh客户端和服务端的配置。
比如，若想改变登录的提示语，则在sshd_config配置文件中找到 Banner 所在的行，删除左边的注释符 # ，并将希望显示的内容所在文件地址跟着Banner后面：
```
Banner /etc/ssh/prompt_text_file
```
配置文件 /etc/ssh/sshd_config 中还可以找到端口号、空闲超时时间等配置项。


**参考资料：**
1. [SSH - 维基百科](https://zh.wikipedia.org/zh-hans/Secure_Shell)）
1. [ssh（安全外壳协议）- 百度百科](https://baike.baidu.com/item/SSH/10407)
1. [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
1. [OpenSSH](https://www.openssh.com/)

