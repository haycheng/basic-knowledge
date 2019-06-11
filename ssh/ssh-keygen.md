# ssh-keygen 命令

## 简介
ssh-keygen 命令用于认证密钥的生成、管理与转换。

## 常见用法

### 生成密钥对
-t选项用于在创建密钥对时指定密钥类型，可用类型包括
- dsa
- ecdsa
- ed25519
- rsa: 
例如，
```
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/haycheng/.ssh/id_rsa): /Users/haycheng/.ssh/id_rsa_0
```
这里询问你即将生成的密钥存储位置，如果不输入文件路径而直接回车，则会将密钥存储在默认的`/Users/haycheng/.ssh/id_rsa`文件中。我这里输入的是`/Users/haycheng/.ssh/id_rsa_0`。
接线来询问你使用密钥时的口令短语（passphrase），输入之后，以后用到该密钥时，需要输入口令验证；也可以直接回车不输入，即每次使用该密钥时不进行口令验证。
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
此命令会要求你输入密码(可以留空), 然后就会生成密钥并会显示一张该密钥对应的随机图.

### 
