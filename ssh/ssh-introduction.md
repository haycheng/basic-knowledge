# SSH (Secure SHell) 介绍

## 
SSH 是 Secure Shell 的缩写，意为安全外壳协议。它是位于应用层的一种加密的网络传输协议（通过对传输数据进行加密来保证security），用于为远程登录和其他网络服务提供安全的通信环境。虽然任何网络服务都可以通过SSH实现安全传输，但最常见的用途是远程登录系统，人们通常利用SSH来传输命令行界面和远程执行命令（[SSH - 维基百科](https://zh.wikipedia.org/zh-hans/Secure_Shell)）。

SSH只是一种协议，存在多种实现，既有商业实现，也有开源实现。OpenSSH就是SSH协议的一个著名开源实现，应用非常广泛，很多操作系统和产品（详细名单见[这里](https://www.openssh.com/users.html)）都使用了该软件，因此学会使用OpenSSH工具套件很重要。

OpenSSH 的实现中，包含了如下几类工具：
* 远程操作：ssh, scp, sftp
* 密钥管理：ssh-add, ssh-keysign, ssh-keyscan, ssh-keygen
* 服务端相关：sshd, sftp-server, ssh-agent

## ssh命令
ssh命令是OpenSSH套件中的客户端连接工具，用于安全地连接远程主机。在远程主机中，运行着一个名为sshd的进程，该进程默认监听着22端口，等待用户使用ssh客户端进行连接。

ssh的用法如下：
```
     ssh [-1246AaCfgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec] [-D  [bind_address:]port] [-e escape_char]
         [-F configfile] [-i identity_file] [-L  [bind_address:]port:host:hostport] [-l login_name] [-m mac_spec]
         [-O ctl_cmd] [-o option] [-p port] [-R  [bind_address:]port:host:hostport] [-S ctl_path]
         [-w local_tun[:remote_tun]] [user@]hostname [command]
```
各选项的具体含义，可以参看man文档。这里介绍下基本的用法。

假设 haycheng 是主机 myhost.com（假定其IP为 3.14.159.26）上的一个用户，则可以通过如下命令远程登录主机：
```
ssh haycheng@myhost.com
```
或通过IP地址登录：
```
ssh haycheng@3.14.159.26
```
如果本地的用户名与用于远程登录的用户名一致（即均为 haycheng），则上述命令中的“haycheng@”可以省略。

如果不是连接默认的22端口，则可通过-p指定连接的端口号：
```
ssh -p 1234 haycheng@3.14.159.26
```
## 第一次登录
用户在第一次登录某个主机host时，会出现类似如下的提示：
```
$ ssh haycheng@3.14.159.26
The authenticity of host '3.14.159.26 (3.14.159.26)' can't be established.
RSA key fingerprint is SHA256:rgUYhGbCjHk+Qv2EbzOFBlJmKWy2vu3EPTMy5kZHHA4.
Are you sure you want to continue connecting (yes/no)?
```
这是因为第一次登录该主机，无法确认主机的真实性、可靠性。
接着打印出了该主机的RSA公钥的指纹（用SHA256算法计算出了公钥的摘要，便于比对），通过该指纹来验证主机是否真实可靠（跟该主机在网站上贴出的公钥指纹进行核对）。
如果能够确认主机是可靠的，则输入yes进行连接，会显示如下信息：
```
Warning: Permanently added '100.69.41.125' (RSA) to the list of known hosts.
```
表明将该主机加入了用户的已知主机列表（~/.ssh/known_hosts）中。下次连接该主机时，ssh发现主机的公钥已经保存在known_hosts中了，就不会显示确认连接的信息，直接提示输入密码进行连接[^1]。

每个SSH用户都有自己的known_hosts文件，此外系统也有一个这样的文件，通常是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。

## 两种登录方式
通过ssh登录远程主机时，有两种方式可以选择：密码登录和公钥登录。
### 密码登录
每次登录远程主机，都需要输入使用的远程主机上的用户名对应的密码。
### 公钥登录


~/.ssh/config中有个配置项`StrictHostKeyChecking`，取值可为 yes 或 no，表示采用或不采用严格的主机key检测。
当出现
有以下两个解决方案：
1. 手动删除修改known_hsots里面的内容；
2. 修改配置文件“~/.ssh/config”，加上这两行，重启服务器。
   StrictHostKeyChecking no
   UserKnownHostsFile /dev/null

优缺点：
1. 需要每次手动删除文件内容，一些自动化脚本的无法运行（在SSH登陆时失败），但是安全性高；
2. SSH登陆时会忽略known_hsots的访问，但是安全性低；
--------------------- 
作者：yasaken 
来源：CSDN 
原文：https://blog.csdn.net/yasaken/article/details/7348441 
版权声明：本文为博主原创文章，转载请附上博文链接！

**参考资料：**
[^1]: [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
[^2]: [OpenSSH](https://www.openssh.com/)

