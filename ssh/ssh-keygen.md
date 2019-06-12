# ssh-keygen 命令

## 简介
ssh-keygen 命令用于认证密钥的生成、管理与转换。

## 常见用法

### 生成密钥对
`-t dsa | ecdsa | ed25519 | rsa` 选项用于在创建密钥对时指定密钥类型，可用类型包括
- dsa
- ecdsa
- ed25519
- rsa: 

`-C comment` 用于在生成的密钥中添加注释/描述，该注释可以在公钥文件的末尾看到。如果没有显式添加注释，则默认注释的形式为“当前用户名@本地主机名”。

例如，
```
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/haycheng/.ssh/id_rsa): /Users/haycheng/.ssh/id_rsa_0
```
这里询问你即将生成的密钥存储位置，如果不输入文件路径而直接回车，则会将密钥存储在默认的`/Users/haycheng/.ssh/id_rsa`文件中。我这里输入的是`/Users/haycheng/.ssh/id_rsa_0`。

接下来询问你使用密钥时的口令短语（passphrase）。输入口令之后，以后用到该密钥ssh登录时，都需要输入口令验证；也可以直接回车不输入，即每次使用该密钥时无需口令验证。
```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/haycheng/.ssh/id_rsa_0.
Your public key has been saved in /Users/haycheng/.ssh/id_rsa_0.pub.
The key fingerprint is:
SHA256:MK02tNea/mEgZrFKmgaqqlJFn/WIiCUT4k8LT4YTiCA chengliangxi@chengliangxideMacBook-Pro.local
The key's randomart image is:
+---[RSA 2048]----+
|E...             |
|=.= o  ..        |
| = X o=+.o       |
|  X +.+B...      |
|.  =. X S .      |
|...+ = + +       |
|..+ .   o o      |
|o.     . . .     |
|B       ...      |
+----[SHA256]-----+
```
从显示的文本中可以看到，ssh-keygen生成了一个rsa类型的密钥对，其中私钥存储在`/Users/haycheng/.ssh/id_rsa_0`文件中，公钥存储在`/Users/haycheng/.ssh/id_rsa_0.pub`文件中。
最后会生成密钥并会显示一张该密钥对应的随机图。

### 搜索主机密钥
`ssh-keygen -F hostname` 用于在 ~/.ssh/known_hosts 文件中寻找主机 hostname 对应的密钥。

### 删除主机密钥
如果主机地址更改或者不再使用某个密钥, 可以通过`ssh-keygen -R hostname`删除 known_hosts 文件中指定主机的密钥，而不必手动在 known_hosts 文件中进行删除。

## 参考文档
1. [Linux 下 SSH 命令实例指南](https://linux.cn/article-3858-1.html)

