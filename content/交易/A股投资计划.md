---
title: "A股投资计划"
date: 2021-06-07T21:10:00+08:00
publishDate: 2021-06-21T00:00:00+08:00
authors:
    - "enel"
tags:
    - "持续更新中"
    - "todo"
draft: true
---
## 仓位

2:6:2  
底仓:美股崩盘带下来A股后加仓:灵活

最后一个2可以买个股，其他只能买500ETF。

    之前两次牛市500ETF比300ETF涨幅大。
    行业ETF、个股不知道怎么选。

## 个股

选股：

- [第七章：最重要的日内交易策略](https://docs.google.com/document/d/1haZC4yHW9okyQHJIJ-b8MPhs6OyliRS6Mx28x8Fg95M/edit#heading=h.szq52nxhzkxt)
- 用下面的指标找规律，制定相应的止损止盈策略（todo）。

同花顺远航版自定义指标[文档](http://quant.10jqka.com.cn/platform/html/help-api-yuan.html?t=-10#843/0)。

### 智能选股

todo

### 连续阴线或阳线数

``` python
COUNT_MAX_LOWER = 6    # 配置：最多连续阴线数
COUNT_MAX_UPPER = 6    # 配置：最多连续阴线数 
######################
count=0
def isExtreme(count):
    return True if (count>=COUNT_MAX_UPPER or count<=-1*COUNT_MAX_UPPER) else False
for i in range(0, total):
    count=0
    for j in range(i, 0, -1):
        o=get("开盘价", j)
        c=get("收盘价", j)
        if(c>=o):
            if(count>=0):
                count+=1
            else:
                save("连续（极限）" if(isExtreme(count)) else "连续", count, i)
                break;
        if(c<=o):
            if(count<=0):
                count-=1
            else:
                save("连续（极限）" if(isExtreme(count)) else "连续", count, i)
                break;
draw.line(0, begin, 0, end, "#808080")
draw.line(COUNT_MAX_UPPER, begin, COUNT_MAX_UPPER, end, "#808080")
draw.line(COUNT_MAX_LOWER*-1, begin, COUNT_MAX_LOWER*-1, end, "#808080")
draw.stick("连续", "#2196F3", 2)
draw.stick("连续（极限）", "#FF9800", 2)
```

### 连续涨或跌天数

``` python
COUNT_MAX_LOWER = 6    # 配置：最多连续下跌天数
COUNT_MAX_UPPER = 6    # 配置：最多连续上涨天数 
######################
count=0
def isExtreme(count):
    return True if (count>=COUNT_MAX_UPPER or count<=-1*COUNT_MAX_UPPER) else False
for i in range(0, total):
    count=0
    for j in range(i, 0, -1):
        c=get("收盘价", j)
        yc=get("昨收价", j)
        if(c>=yc):
            if(count>=0):
                count+=1
            else:
                save("连续（极限）" if(isExtreme(count)) else "连续", count, i)
                break;
        if(c<=yc):
            if(count<=0):
                count-=1
            else:
                save("连续（极限）" if(isExtreme(count)) else "连续", count, i)
                break;
draw.line(0, begin, 0, end, "#808080")
draw.line(COUNT_MAX_UPPER, begin, COUNT_MAX_UPPER, end, "#808080")
draw.line(COUNT_MAX_LOWER*-1, begin, COUNT_MAX_LOWER*-1, end, "#808080")
draw.stick("连续", "#2196F3", 2)
draw.stick("连续（极限）", "#FF9800", 2)
```

---

![股市跳大神](/img/2021-06-07-stock-market-trend.jpg)