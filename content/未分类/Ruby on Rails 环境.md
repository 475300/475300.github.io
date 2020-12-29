---
title: "Windows 10 Ruby 开发环境(wsl2)"
date: 2020-12-29T14:07:26+08:00
draft: false
---
启用wsl，切换到wsl 2，安装openSUSE15.2。

配置openSUSE：

``` bash
# 设置代理 https://www.suse.com/support/kb/doc/?id=7006845
sudo vim /etc/sysconfig/proxy
# 如果是用的[wsl2](https://docs.microsoft.com/en-us/windows/wsl/install-win10#set-your-distribution-version-to-wsl-1-or-wsl-2)的话，代理地址填 `192.168.x.x`

sudo zypper in zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
# 禁用ipv6：https://rvm.io/rvm/security#ipv6-issues

# install rvm
# install ruby

# Rails
`rails server`默认用3000端口，但win10用不了这端口，这么改一下：`rails server -p 5000`
```

Windows下RubyMine添加remote Ruby SDK：[Configure WSL as a remote interpreter](https://www.jetbrains.com/help/ruby/configuring-remote-interpreters-using-wsl.html#wsl_remote)。
