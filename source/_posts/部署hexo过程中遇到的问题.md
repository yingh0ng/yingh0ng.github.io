---
title: 部署hexo过程中遇到的问题
date: 2018-06-11 12:29:45
tags: 工具

---



### GPG加密失败

在github上配置好ssh key后使用命令hexo deploy后出现一下错误：

```shell
gpg: skipped “xxx xxx@xxx.com“: secret key not available
gpg: signing failed: secret key not available
error: gpg failed to sign the data
fatal: failed to write commit object
```

<!-- more -->

进入目录C:\Users\Administrator.gnupg后发现pubring.gpg和secring.gpg这两个文件大小
均为0kb。可能是我之前配置过gpg加密，然后现在过期了。

使用命令：

```shell
gpg --gen-key
```

然后会依次让你选择key类型、key文件大小、有效时间，最后填写名称、邮件地址和备注（这三个
信息跟github账号无关）。最后输出的信息即为你的gpg密钥，如：
pub 2048G/FB7D735A 2018-06-11 [expires: 2020-06-10]
Key fingerprint = 401B EC71 97B4 AEE4 089E C017 7AE8 6157 FB7D 736A
uid XXX (XX) [XXX@gmail.com](mailto:XXX@gmail.com)
sub 2048G/A6CA85D3 2018-06-11 [expires: 2020-06-10]

其中pub为公钥，FB7D735A为公钥key，sub为私钥，A6CA85D3为私钥key。

使用命令：

```shell
gpg --armor --export yourkey
```

“yourkey”为公钥key或者私钥key。打开github->settings->SSH and GPG KEYS，添加GPG KEY。
把刚才命令输出的信息复制粘贴到这儿。

然后输入：

```shell
git config --global commit.gpgsign.true
git config --global user.signingkey FB7D736A（你自己的key）
```

完成配置。

输入命令：

```shell
gpg --list-secret-keys
```

能看到你配置好的GPG key。

最后重新部署：

```shell
hexo clean
hexo Generate
hexo deploy
```

