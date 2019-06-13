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
`-i identity_file`选项指明用哪个私钥进行登录。如果不指明的话，不同认证类型使用的默认密钥文件为：
- dsa: ~/.ssh/id_dsa
- ecdsa: ~/.ssh/id_ecdsa
- ed25519: ~/.ssh/id_ed25519
- rsa: ~/.ssh/id_rsa
也可以在ssh的配置文件中指明主机名与私钥文件的对应关系。

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

## 两种登录方式
通过ssh登录远程主机时，有两种方式可以选择：密码登录和公钥登录。

### 密码登录
每次登录远程主机，都需要输入使用的远程主机上的用户名对应的密码。

ssh中使用密码登录时，数据交换的过程是这样的：
1. 远程主机收到用户的登录请求后，将自己的公钥发送给用户。
1. 用户使用该公钥将自己的登录密码加密，得到密码的密文，然后发送给远程主机。
1. 远程主机用自己的私钥，对登录密码解密。如果密码正确，则同意用户登录，否则登录失败。

在这个过程中，由于采用了公钥加密，所以数据是安全的。但实施过程中存在一个风险，就是如何确定发送公钥给用户的主机，是用户想登录的真正主机。如果中间有个骗子截获了登录请求（比如在公共的wifi区域），然后冒充远程主机，将自己的公钥发给用户。用户根据这个骗子的公钥对自己的登录密码进行加密，发送给骗子后，骗子就可以解密得到用户的密码了。这样，骗子就可以拿着用户的登录名和密码，去做坏事了。

这个风险应该怎么解决呢？因为不像https协议，SSH协议的公钥是没有证书中心（CA）公证的，都是自己签发的。没有了公证人，那么就只能靠用户自己去辨别公钥的真伪了。用户可以在要登录的站点官网上查看站点的公钥（可能是公钥的摘要），只要远程主机发送回来的公钥与官网上的一致（ssh客户端会将收到的远程主机的公钥打印出来，并询问用户该公钥是否可信），就可以放心地与该主机进行连接了（即使发送该可信公钥的是个骗子，他获取了登录密码的密文也没用，因为骗子手里不可能有该可信公钥对应的私钥，也就无法解密获得用户的登录密码）。

### 第一次登录
用户在第一次登录某个主机host时，会出现类似如下的提示：
```
$ ssh haycheng@3.14.159.26
The authenticity of host '3.14.159.26 (3.14.159.26)' can't be established.
RSA key fingerprint is SHA256:rgUYhGbCjHk+Qv2EbzOFBlJmKWy2vu3EPTMy5kZHHA4.
Are you sure you want to continue connecting (yes/no)?
```
这是因为第一次登录该主机，无法确认主机的真实性、可靠性。
接着打印出了该主机RSA公钥的指纹（用SHA256算法计算出了公钥的摘要，便于比对），通过该指纹来验证主机是否真实可靠（跟该主机在网站上贴出的公钥指纹进行核对）。
如果能够确认主机是可靠的，则输入yes进行连接后，会显示如下信息：
```
Warning: Permanently added '3.14.159.26' (RSA) to the list of known hosts.
```
表明将该主机加入了用户的已知主机列表（`~/.ssh/known_hosts`）中。下次连接该主机时，ssh发现主机的公钥已经保存在known_hosts中了，就不会显示确认连接的信息，直接提示输入密码进行连接。

除了每个SSH用户有自己单独的known_hosts文件外，系统也可以有全局的known_hosts文件（如果存在，通常是`/etc/ssh/ssh_known_hosts`），存放对所有用户都可信的远程主机公钥。

**待验证**如果有多个Linux系统使用同一IP，而这些系统的公钥是不同的。在登录一次后，本地就会把IP及其对应的公钥记录在known_hsots文件中，那么当远程主机切换到另一个系统后，远程主机的公钥变了，这时再次用ssh登录该IP时，就会出现公钥不一致的警告（`WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!`），这时要么手动删除known_hsots里面对应该IP的内容，要么修改配置文件“~/.ssh/config”，加上`StrictHostKeyChecking no`和`UserKnownHostsFile /dev/null`两项配置，然后重启服务（这种方法降低了安全性）。

### 公钥登录
用密码登录时，需要每次都输入密码，而SSH的公钥登录，不必密码即可登录，相对来说要便捷很多。

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。用户登录的时候会带上自己的公钥，远程主机从该用户在远程主机的家目录下`~/.ssh/authorized_keys`查找是否存在该公钥。如果存在的话，则会向用户发送一段随机字符串，用户用自己的私钥加密后，再发给远程主机。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求输入密码。

公钥登录的方式，需要用户事先创建好一个密钥对（用`ssh-keygen`生成密钥对的方法见[这里]()）。然后，用`ssh-copy-id`将指定的公钥发送给远程主机，这样下次就可以通过公钥登录了。

`ssh-copy-id`是ssh套件提供的一个工具，用来将本地的公钥传送给远程主机，远程主机将接收到的公钥上保存在用户家目录的`~/.ssh/authorized_keys`文件中，下次就可以通过检查`authorized_keys`文件来确定能否用公钥登录了。`ssh-copy-id`的基本用法是通过`-i`选项指定要传送的公钥文件（以.pub结尾。即使文件没有以.pub结尾，工具也会查找加上.pub之后的文件），然后与`ssh`命令的用法类似，需要指明远程主机与登录的用户名，即`ssh-copy-id -i identity_file user@]hostname`。其他更多用法请查看man文档。
```
$ ssh-copy-id -i ~/.ssh/id_rsa_0 joe.clx@3.14.159.26
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/haycheng/.ssh/id_rsa_0.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
joe.clx@3.14.159.26's password:

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'haycheng@3.14.159.26'"
and check to make sure that only the key(s) you wanted were added.
```

**待验证**如果在将公钥发送给远程主机后，用户仍然无法利用公钥登录，则需要更改远程主机sshd进程的配置。sshd进程的配置文件位置是`/etc/ssh/sshd_config`，检查是否配置如下：
```
　　RSAAuthentication yes
　　PubkeyAuthentication yes
　　AuthorizedKeysFile .ssh/authorized_keys
```
其中，RSAAuthentication 用来设置是否开启RSA密钥验证，只针对SSH1；PubkeyAuthentication 用来设置是否开启公钥验证，如果使用公钥验证的方式登录时，则设置为yes；AuthorizedKeysFile 用来设置公钥验证文件的路径，与PubkeyAuthentication配合使用，默认值是".ssh/authorized_keys"。

更改 sshd_config 后，需要重启sshd服务才能生效：
```
　　// ubuntu系统
　　service ssh restart

　　// debian系统
　　/etc/init.d/ssh restart
```

**参考文档**
1. [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
1. [ssh登陆之忽略known_hosts文件](https://blog.csdn.net/yasaken/article/details/7348441)

