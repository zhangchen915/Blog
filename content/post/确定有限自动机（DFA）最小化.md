---
title: "确定有限自动机（DFA）最小化"
categories: ["默认分类"]
tags: []
draft: false
slug: "743"
date: "2019-12-08 18:12:00"
---

<a href="https://img.bi-bo.cn/2019/12/2859639724.svg">StatechartDiagram1.svg</a>



预备
最简化的DFA：这个DFA没有多余状态、也没有两个相互等价的状态。一个DFA可以通过消除无用状态、合并等价状态而转换成一个与之等价的最小状态的有穷自动机。
无用状态：从自动机开始状态出发，任何输入串也发到达的那个状态，或者这个状态没有通路可达终态。
等价转态：两个状态，识别相同的串，结果都同为正确或错误，这两个状态就是等价的。
区别状态：不是等价状态。

```text
// 基于等价类的思想
split(S) 
    foreach (character c) 
        if (c can split S)
            split S into T1, ..., TK

hopcroft() 
    split all nodes into N, A
    while (set is still changes) 
        split(all S)
```

根据非终结状态与非终结状态将所有的节点分为 N 和 A 两大类。 N 为非终结状态，A 为终结状态，之后再对每一组运用基于等价类实现的切割算法。

### 增广路径
从一个未匹配点出发，走交替路，如果途径另一个未匹配点（出发的点不算），则这条交替路称为增广路（agumenting path）。

增广路径性质：
    （1）P的路径长度必定为奇数，第一条边和最后一条边都不属于M，因为两个端点分属两个集合，且未匹配。
    （2）P经过取反操作可以得到一个更大的匹配M’。
    （3）M为G的最大匹配当且仅当不存在相对于M的增广路径。

### 参考文章：
http://spiritsaway.info/hopcroft-karp-algorithm.html
https://www-m9.ma.tum.de/graph-algorithms/matchings-hopcroft-karp/index_en.html 
