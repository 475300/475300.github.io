---
title: "A股年报分词找稀有概念[未完成]"
date: 2020-10-09T19:15:00+08:00
draft: false
---
>> a 股
>>
>> 2020年半年报营收xx亿，稀有概念创造的收入占x%，创造的净利润是xx，创造的营收是xx，是否因为疫情影响，是否有持续性和复制性等等
>
> 类似这样

盒饭群九月底要交的作业，每人从A股里找10个概念稀缺的股票。

## 思路

**抓取所有A股最近的半年报告，分词，去重、留下业务相关的名词，统计该股半年报中每个词出现在几个股票的半年报中。**

出现次数少的业务名词可能就是稀缺概念，挨个研究。

## 找所有A股的代码

随便找了[一个](http://www.shdjt.com/js/lib/astock.js)。

## 抓年报

新浪财经有各股票的半年报列表，以人福药业（600079）为例：`http://money.finance.sina.com.cn/corp/go.php/vCB_Bulletin/stockid/600079/page_type/ndbg.phtml`，替换`600079`就能得到其它股票的半年报列表。

用[pyspider](https://github.com/binux/pyspider)把所有股票这个列表的第一条里的半年报全爬了。

``` python
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
# Created on 2020-09-28 13:44:18
# Project: hefan

from pyspider.libs.base_handler import *

class Handler(BaseHandler):
    crawl_config = {
    }

    def on_start(self):
        symbols = ['300001', '300002']  # 要爬的股票代码贴这儿。
        for s in symbols:
            self.crawl(f'http://vip.stock.finance.sina.com.cn/corp/go.php/vCB_Bulletin/stockid/{s}/page_type/ndbg.phtml', callback=self.index_page)

    def index_page(self, response):
        self.crawl(response.doc('.datelist > ul > a').attr.href, callback=self.detail_page)

    @config(priority=2)
    def detail_page(self, response):
        return {
            "url": response.url,
            "title": response.doc('#allbulletin > thead > tr > th').text(),
            "content": response.doc('#content').text(),
        }
```

## 分词

- ~~[结巴中文分词](https://github.com/fxsjy/jieba)~~    → 效果不好
- ~~[pullword](http://api.pullword.com/)~~             → 效果最好，但api有限制，也不稳定。
- [HanLP](https://github.com/hankcs/HanLP)             ✓

``` python
import hanlp
import json

tokenizer = hanlp.load('LARGE_ALBERT_BASE')
tagger = hanlp.load(hanlp.pretrained.pos.CTB5_POS_RNN_FASTTEXT_ZH)

pipeline = hanlp.pipeline()\
    .append(hanlp.utils.rules.split_sentence, output_key='sentences')\
    .append(tokenizer, output_key='tokens')\
    .append(tagger, output_key='part_of_speech_tags')

# 1. 筛名词
# NN 普通名词，NR 专有名词
# 2. 名词去重
# 3. ｛"002468":["枸橼酸芬太尼", "枸橼酸舒芬太尼", "注射液"]}
# 4. 查某一名词出现在了几只股票里。

with open('C:\\Users\\-\\Desktop\\hefan\\hanlp\\hefan_300xxx.json', encoding='utf-8') as f1, open('C:\\Users\\-\\Desktop\\hefan\\hanlp\\hefan_300xxx_parsed.txt', 'a', encoding='utf-8') as f2:
    for line in f1:
        code = line[136:142]    # 股票代码
        doc = pipeline(line)
        tokens = doc.to_dict()["tokens"]
        part_of_speech_tags = doc.to_dict()["part_of_speech_tags"]

        tmp_tokens = []
        for i in range(len(tokens)):
            for j in range(len(tokens[i])):
                if(part_of_speech_tags[i][j] in ["NN", "NR"]):
                    tmp_tokens.append(tokens[i][j])

        tokens = list(set(tmp_tokens))  # 去重
        f2.write(f"{code}-"+",".join(tokens)+"\n")
```

要安装cuda和cudnn。

## TODO

---

- docker 省事。
- 分词真慢。代码是`688xxx`的股票的半年报文本有35.8M，分词+去重用了5小时45分钟。其余的半年报文本有1361M。
  - E3-1231v3 GTX970 8Gx2
  - cuda 10.1.243, cudnn 7.6.5.32
