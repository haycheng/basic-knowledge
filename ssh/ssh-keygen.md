# ssh-keygen 命令

## 简介
ssh-keygen 命令用于认证密钥的生成、管理与转换。

## 生成密钥
由于ssh用的是公钥加密技术，所以生成密钥都是成对的“私钥+公钥”。用 ssh-keygen 生成的密钥文件中，公钥文件名的形式为私钥文件名加“.pub”后缀，因此只要私钥文件名确定，公钥文件名也就确定了。
`-t`选项用于在创建密钥对时指定密钥类型，可用类型包括
- dsa
  > 生成的私钥文件名默认为~/.ssh/id_dsa，公钥文件中的内容以 ssh-dss 开头（也有说以 ssh-dsa 开头的，但我产生的公钥以 ssh-dss 开头）。
- ecdsa
  > 生成的私钥文件名默认为~/.ssh/id_ecdsa，公钥文件中的内容以 ecdsa-sha2-nistp256 （使用默认椭圆曲线大小256时）开头。
- ed25519
  > 生成的私钥文件名默认为~/.ssh/id_ed25519，公钥文件中的内容以 ssh-ed25519 开头。
- rsa
  > 生成的私钥文件名默认为~/.ssh/id_rsa，公钥文件中的内容以 ssh-rsa 开头。

`-b bits`选项用于指定生成的密钥的比特数(bit)。对于RSA密钥，最小位数为1024比特，默认为2048比特；DSA密钥只能为1024比特；对于ECDSA密钥，该值指定椭圆曲线的大小，只能为256（默认）、384或521；Ed25519密钥的位数是固定的，无需指定，该选项无效。

`-C comment` 用于在生成的密钥中添加注释/描述，该注释可以在公钥文件的末尾看到。当有多个用于不同站点的密钥时，可以通过注释进行区分。通常做法是以"your_email@example_site.com"作为注释，如果没有指明注释，则默认注释的形式为“当前用户名@本地主机名”。

例如：
```
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/haycheng/.ssh/id_rsa): /Users/haycheng/.ssh/id_rsa_0
```
这里询问你即将生成的**私钥**文件名，如果不输入文件路径而直接回车，则会将私钥存储在默认的`/Users/haycheng/.ssh/id_rsa`文件中。我这里输入的是`/Users/haycheng/.ssh/id_rsa_0`。也可以直接通过`-f filename`选项来指定待生成的私钥文件。

接下来需要你为私钥文件的使用设置口令短语（passphrase）。输入口令之后，以后在ssh登录中用到该私钥时，需要输入口令验证；也可以直接回车不输入，即每次使用该密钥时无需口令验证。
```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/haycheng/.ssh/id_rsa_0.
Your public key has been saved in /Users/haycheng/.ssh/id_rsa_0.pub.
The key fingerprint is:
SHA256:MK02tNea/mEgZrFKmgaqqlJFn/WIiCUT4k8LT4YTiCA haycheng@haychengdeMacBook-Pro.local
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
从显示的文本中可以看到，ssh-keygen生成了一个rsa类型的密钥对，其中私钥存储在`/Users/haycheng/.ssh/id_rsa_0`文件中，公钥存储在`/Users/haycheng/.ssh/id_rsa_0.pub`文件中（以.pub为后缀）。公钥可以给任何人读取，但私钥只有拥有者才有读取权限，其他人都不应该有权限读。

最后会生成密钥并会显示一张该密钥对应的随机图。

## 设置/更改私钥口令
如果想更改为私钥文件设置的口令，可以用`-p`选项。为了指定更改哪个私钥文件，在`-f`后面跟上需要更改口令的私钥文件名。如果不用`-f`指定，则需在后续的交互中指定私钥文件。
```
$ ssh-keygen -f ~/.ssh/id_rsa_0 -p
Enter old passphrase:
Key has comment 'haycheng@haychengdeMacBook-Pro.local'
Enter new passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved with the new passphrase.
```

## 搜索主机密钥
`ssh-keygen -F hostname` 用于在 ~/.ssh/known_hosts 文件中寻找主机 hostname 对应的密钥。

## 删除主机密钥
如果主机地址更改或者不再使用某个密钥, 可以通过`ssh-keygen -R hostname`删除 known_hosts 文件中指定主机的密钥，而不必手动在 known_hosts 文件中进行删除。

## 参考文档
1. [Linux 下 SSH 命令实例指南](https://linux.cn/article-3858-1.html)

