---
title: "Android源码"
date: 2021-04-06T18:31:00+08:00
authors:
    - "enel"
draft: true
---

在线看 [https://cs.android.com/](https://cs.android.com/)

## 下载源码

[谷歌源](https://source.android.com/setup/build/downloading)、[中科大源](https://mirrors.ustc.edu.cn/help/aosp.html)（速度快）、[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)

用Git Bash，Windows Terminal、cmd运行不了`repo`。WSL性能太差，没法用。

``` bash
repo init -u https://android.googlesource.com/platform/manifest -b android-security-8.1.0_r86
```

``` bash
repo sync
```

---

有些文件名字里有冒号，windows里没法创建这样的文件名。所以还是在线看吧。
