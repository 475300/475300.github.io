---
title: "readme"
date: 2021-04-25T21:58:00+08:00
authors:
    - "enel"
tags: 
    - "todo"
draft: false
---
[乐土为家]

## 如何配置的

GitHub Pages + HUGO + [The Root's Home 主题]

配置Github Actions（[gh-pages.yml]），使得每次push到master后执行hugo命令更新网站。

(本地预览时用`hugo server --minify`命令，跟 gh-pages.yml 中保持一致。)

### Netlify CMS

按[这个](https://www.netlifycms.org/docs/add-to-your-site/)配置。

> 没什么用。

## 如何维护

### 同步代码

主题是子模块，所以`git pull --recurse-submodules`。

### 发新文章

1. 用git。
2. 用[Netlify CMS](https://475300.github.io/admin)。
    - 在[这里](https://github.com/475300/475300.github.io/blob/master/static/admin/config.yml)配置CMS。

### shortcodes

hugo可以用[shortcodes](https://gohugo.io/content-management/shortcodes/)扩展markdown，官方内置了[这些](https://gohugo.io/content-management/shortcodes/)，我们添加了下面这些：

#### video

``` md
{{</*video src="/img/2020-09-09-猫-02.mp4" width="200px"*/>}}
```

{{< video src="/img/2020-09-09-猫-02.mp4" width="200px">}}

#### mermaid

→ [官方文档](https://mermaid-js.github.io/mermaid/#/)

```md
{{</*mermaid*/>}}
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
{{</*/mermaid*/>}}
```

{{<mermaid>}}
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
{{</mermaid>}}

#### gallery

→ [官方文档](https://github.com/liwenyip/hugo-easy-gallery)

```md
{{</*allery*/>}}
{{</*igure src="/img/2021-05-27-launcher-1.webp" width="110px" caption="launcher-1"*/>}}
{{</*igure src="/img/2021-05-27-launcher-2.webp" width="110px" caption="launcher-2"*/>}}
{{</*igure src="/img/2021-05-27-launcher-3.webp" width="110px" caption="launcher-3"*/>}}
{{</*igure src="/img/2021-05-27-launcher-4.webp" width="110px" caption="launcher-4"*/>}}
{{</*/gallery*/>}}
{{</*load-photoswipe*/>}}
```

{{<gallery>}}
{{<figure src="/img/2021-05-27-launcher-1.webp" width="110px" caption="launcher-1">}}
{{<figure src="/img/2021-05-27-launcher-2.webp" width="110px" caption="launcher-2">}}
{{<figure src="/img/2021-05-27-launcher-3.webp" width="110px" caption="launcher-3">}}
{{<figure src="/img/2021-05-27-launcher-4.webp" width="110px" caption="launcher-4">}}
{{</gallery>}}
{{<load-photoswipe>}}

## todo

见[博客-Trello]。

[乐土为家]: https://475300.github.io/

[The Root's Home 主题]: https://github.com/475300/the-roots-home

[gh-pages.yml]: https://github.com/475300/475300.github.io/blob/master/.github/workflows/gh-pages.yml

[博客-Trello]:  https://trello.com/b/qkqzqWLU/%E5%8D%9A%E5%AE%A2