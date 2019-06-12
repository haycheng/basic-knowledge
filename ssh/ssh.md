# ssh 命令

## 简介
ssh命令是OpenSSH套件中的客户端连接工具，用于安全地连接远程主机。在远程主机中，运行着一个名为sshd的进程，该进程默认监听着22端口，等待用户使用ssh客户端进行连接。

ssh的用法如下：
```
     ssh [-1246AaCfgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec] [-D  [bind_address:]port] [-e escape_char]
         [-F configfile] [-i identity_file] [-L  [bind_address:]port:host:hostport] [-l login_name] [-m mac_spec]
         [-O ctl_cmd] [-o option] [-p port] [-R  [bind_address:]port:host:hostport] [-S ctl_path]
         [-w local_tun[:remote_tun]] [user@]hostname [command]
```
各选项的具体含义，可以参看man文档。这里介绍下基本的常见用法。


## 常见用法
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
### 远程执行命令
如果只想在远程主机上执行一次命令，则可以在ssh命令后面跟上想在远程主机上执行的命令，命令执行完后从远程主机退出，执行结果会回显在终端：
```
ssh haycheng@3.14.159.26 pwd
```
终端显示如下：
```
haychengdeMacBook-Pro:~ haycheng$ ssh haycheng@3.14.159.26 pwd
Enter passphrase for key '/Users/haycheng/.ssh/id_rsa':
/home/haycheng
```
如果ssh后面跟的是`bash`，则会进入一个bash的交互式命令环境，可以输入各种想在远程主机执行的命令。

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

ssh中使用密码登录时，数据交换的过程是这样的：
1. 远程主机收到用户的登录请求后，将自己的公钥发送给用户。
1. 用户使用该公钥将自己的登录密码加密，得到密码的密文，然后发送给远程主机。
1. 远程主机用自己的私钥，对登录密码解密。如果密码正确，则同意用户登录，否则登录失败。

在这个过程中，由于采用了公钥加密，所以数据是安全的。但实施过程中存在一个风险，就是如何确定发送公钥给用户的主机，是用户想登录的真正主机。如果中间有个骗子截获了登录请求（比如在公共的wifi区域），然后冒充远程主机，将自己的公钥发给用户。用户根据这个骗子的公钥对自己的登录密码进行加密，发送给骗子后，骗子就可以解密得到用户的密码了。这样，骗子就可以拿着用户的登录名和密码，去做坏事了。

这个风险应该怎么解决呢？因为不像https协议，SSH协议的公钥是没有证书中心（CA）公证的，也就是说都是自己签发的。没有了公证人，那么就只能靠用户自己去辨别公钥的真伪了。用户可以在要登录的站点官网上查看站点的公钥（可能是公钥的摘要），只要远程主机发送回来的公钥与官网上的一致（ssh客户端会将收到的远程主机的公钥打印出来，并询问用户该公钥是否可信），就可以放心地与该主机进行连接了（即使发送该可信公钥的主机是个骗子，那他获取登录密码的密文也没用，因为骗子手里不可能有该可信公钥对应的私钥，也就无法解密获得用户的登录密码）。

可以设想，如果攻击者插在用户与远程主机之间，用伪造的公钥，获取用户的登录密码。再用这个密码登录远程主机，那么SSH的安全机制就荡然无存了。这种风险就是著名的"中间人攻击"（Man-in-the-middle attack）。

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
