---
title: "配置Oracle VPS"
date: 2021-05-24T15:29:04+08:00
authors:
    - "enel"
tags:
    - "Linux"
    - "Ubuntu"
    - "iptables"
    - "防火墙"
    - "VPS"
draft: false
---

按这篇[文章](https://xunihao.net/867.html#Oracle-CloudAWS)申请了免费的Oracle Cloud、配置了子网的安全规则。

## 配置VPS

免费的只支持 Ubuntu、CentOS、Oracle Linux，因为 CentOS 似乎前途暗淡，只能选 Ubuntu(20.04) 了。

创建实例后直接用 Windows Terminal 通过 SSH 登录。

> Windows Terminal的快捷键：  
> `alt+shift++`：竖直分割窗口  
> `alt+shift+-`：水平分割窗口  
> `alt+↑/↓/←/→`：在窗口间移动焦点  
> `alt+shift+↑/↓/←/→`：调整窗口大小

导入私钥时可能需要参考的[链接1](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement#user-key-generation)、[链接2](https://stackoverflow.com/questions/52113738/starting-ssh-agent-on-windows-10-fails-unable-to-start-ssh-agent-service-erro)。

``` bash
# 用SSH登录
ssh ubuntu@168.138.33.146
```

``` bash
# 安装 tldr、zsh、NodeJS、npm：
sudo apt install tldr zsh nodejs npm
```

安装 oh-my-zsh、rvm、Ruby、Rails、Yarn。

因为没有root密码，并且改root密码后不能用，所以无法用`chsh`命令把zsh设置成默认的shell。不过可以参考[chsh -s 到底修改了哪里](http://c.biancheng.net/linux/chsh.html)直接改。

按[Getting Started with Rails](https://guides.rubyonrails.org/getting_started.html#hello-rails-bang)操作到"4.1 Starting up the Web Server"这一步。启动服务器的命令要改为`rails server --binding=0.0.0.0`，否则只能本地访问。

### 配置防火墙

现在这个系统默认带了两个iptables的前端，即netfilter-persistent和ufw，而且默认两个都是enable的。

``` bash
# 查看所有service的状态
service --status-all
```

iptables的规则是有顺序的，用 ufw 的话，因为规则是添加在 netfilter-persistent 添加的规则（就是/etc/iptables/rules.v4）后面，会被后者的规则先匹配到而起不到作用。

又 ufw.service 启动还有问题[^3]，所以不用 ufw。

``` bash
# 这个命令能看到当前的配置
iptables -L -n

# 这个能导出iptables
iptables-save > iptables.bak

# 这个能导入iptables
iptables-restore < iptables.bak
```

``` bash
# 编辑iptables的规则。netfilter-persistent操作的就是这个。
vim /etc/iptables/rules.v4
```

按下面注释说明的修改

    *filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [463:49013]
    :InstanceServices - [0:0]
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p udp --sport 123 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    -A INPUT -p tcp --dport 3000 -j ACCEPT
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    # 在这添加下面这句，开放puma的默认端口3000
    -A INPUT -p tcp --dport 3000 -j ACCEPT
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    -A OUTPUT -d 169.254.0.0/16 -j InstanceServices
    ...

``` bash
# 使修改不重启就生效
iptables-restore < /etc/iptables/rules.v4
```

### 可能用到的其他命令

``` bash
# 切换成root
sudo -i

scp ubuntu@168.138.33.146:/home/ubuntu/iptables.txt .

# 当前shell
echo $0
```

## 其他

配置好后在Oracle Clould的控制台中创建快照： “More Actions-Create Custom Image”。

[^1]: 没细看前面几行的"ACCEPT"为什么没效果。
[^2]: [How to open Ports on Iptables in a Linux server](https://www.e2enetworks.com/help/knowledge-base/how-to-open-ports-on-iptables-in-a-linux-server/)。
[^3]: [Fix ufw service not loading after a reboot](https://devtidbits.com/2019/07/31/ufw-service-not-loading-after-a-reboot/)
