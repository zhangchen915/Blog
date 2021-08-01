---
title: "矩阵相乘的Strassen's算法"
categories: ["算法"]
tags: []
draft: false
slug: "629"
date: "2019-08-17 02:17:00"
---

矩阵乘法的数学表达式为![equation.png][1]

对于两个n*n整数矩阵，相乘需要进行`n^3`次整数乘法和`n^3-n^2`次整数加法，显然时间复杂度为`O(n^3)`。

我们先将问题简化，任意的矩阵乘法我们都可以转换为两个2x2的矩阵相乘。而这个2x2的矩阵乘法可以拆解为如下形式。
![捕获.PNG][2]

Strassen有`(7/8)n^3`次整数乘法和`(7/8)n^3+(3/4)n^2+8`次加法，而整数乘法运算时间大于加法。最终的时间复杂度为`O(n*log2(7))`

**算法的核心思想是用更小规模的乘法来得到中间矩阵，最后加法汇总成需要的矩阵。**

[Introduction and Strassen’s Algorithm.pdf][4]


  [1]: https://img.zhangchen915.com/2019/08/1300790795.png
  [2]: https://img.zhangchen915.com/2019/08/636473684.png
  [3]: https://img.zhangchen915.com/2019/08/3252040619.png
  [4]: https://img.zhangchen915.com/2019/08/2947691384.pdf
