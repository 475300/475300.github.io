---
title: "netfilter-persistent.service 执行流程梳理"
date: 2021-05-24T22:16:29+08:00
authors:
    - "enel"
tags:
    - "Linux"
    - "Ubuntu"
    - "iptables"
    - "systemd"
    - "防火墙"
    - "todo"
draft: false
---
[配置Oracle VPS的防火墙](http://localhost:1313/%E5%BC%80%E5%8F%91/%E9%85%8D%E7%BD%AEoracle-vps/#%E9%85%8D%E7%BD%AE%E9%98%B2%E7%81%AB%E5%A2%99)时遇到了问题，所以顺便看一下。用的时间太长了，这种岔开的细事要先不求甚解。

## netfilter-persistent.service 做了什么

{{<mermaid>}}
graph TD;
    z[?]-->a
    a["/lib/systemd/system/netfilter-persistent.service"] --> b[`/usr/sbin/netfilter-persistent start`<br/>`/usr/sbin/netfilter-persistent stop`]
    b --> |default config| c1["/etc/default/netfilter-persistent"]
    b --> c2["/usr/share/netfilter-persistent/plugins.d"]
    c2 --> d1["./15-ip4tables"]
    c2 --> d2["./25-ip6tables"]
    d1 --> e[`iptables-restore < /etc/iptables/rules.v4`<br/>`iptables-save > /etc/iptables/rules.v4`<br />`iptables -F`<br/>...<br/>]
    d2 --> e
{{</mermaid>}}

## netfilter-persistent.service 是如何启动的

`tldr systemd-analyze` 列出了四个命令，

    ➜  ~ tldr systemd-analyze
    systemd-analyze
    Show timing details about the boot process of units (services, mount points, devices, sockets).

    - List time of each unit to start up:
    systemd-analyze blame

    - Print a tree of the time critical chain of units:
    systemd-analyze critical-chain

    - Create an SVG file showing when each system service started, highlighting the time that they spent on initialization:
    systemd-analyze plot > {{path/to/file.svg}}

    - Plot a dependency graph and convert it to an SVG file:
    systemd-analyze dot | dot -T{{svg}} > {{path/to/file.svg}}

用后两个命令生成图片，用 `scp ubuntu@168.138.53.159:/home/ubuntu/dependency.svg .`命令复制到本地查看。

![各服务启动耗时](/img/2021-05-25-services-init-time.svg)

![各服务依赖](/img/2021-05-25-services-dependency.svg)

依赖图看吐了，图是用 Graphviz 生成的，也可以用Graphviz过滤掉不要的，太晚了不弄了，源头是“*.slice”。至于它是怎么启动的就没再查下去了，有机会的话系统地看下 systemd[^todo]。生成依赖图的源文件是[2021-05-25-services-dependency.dot](/file/2021-05-25-services-dependency.dot)。

[^todo]: - [ ] 系统地分析 systemd。
