title:  在 Mac 上安装 Git
date: 2018-06-21 19:59:59
tags:
- git
- mac
---

有两种方式在 Mac 上安装 Git：
- 安装 homebrew，然后通过 homebrew 安装 Git，具体方法请参考 homebrew 的文档：http://brew.sh/。
- 直接从 AppStore 安装 Xcode，Xcode 集成了 Git，不过默认没有安装，你需要运行 Xcode，选择菜单“Xcode”->“Preferences”，在弹出窗口中找到“Downloads”，选择“Command Line Tools”，点“Install”就可以完成安装了。

安装完 Git 其实还没有 `.ssh` 目录的，要执行生成密钥对的命令以后才会自动生成 `.ssh` 目录，并且新生成的密钥对都在 `.ssh` 目录下了。

要修改密钥的读写权限，否则无法使用 Git。执行以下命令修改权限：
`chmod 644 ~/.ssh/authorized_keys`
如果出现 `Permissions 0644 for ‘/root/.ssh/id_rsa’ are too open. `等错误显示了，只要把权限降到 `0600` 就ok了。
`chmod 0600 /root/.ssh/id_rsa`
然后就可以密钥登录了。
