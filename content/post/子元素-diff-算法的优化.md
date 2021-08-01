---
title: "子元素 diff 算法的优化"
categories: ["前端","算法"]
tags: []
draft: false
slug: "674"
date: "2019-10-06 00:44:00"
---

![5a58d2b2bbd4b-min.jpg][1]
### React 的 Diff
React先遍历新数组来找到插入和需要移动的节点，为了避免不必要的移动，react使用了一个哨兵值lastIndex，记录访问过的源数组下标最大值，如果当前元素的下标小于lastIndex，表示原相对位置无法满足需要了需要移动，否则更新lastIndex，这样来最大限度地利用原相对位置。
最后遍历一遍旧数组找到需要删除的元素。

但这种方式并不是总能得到最优结果。

例如：'1234' ➔ '4123'
React 并没有将4移到最前面，而是将234都移动一次。因为对新数组的遍历是从左向右的依次进行，所以这种位置相对关系总是局部的。


### Diff 的改进

#### 缩小问题规模

首先考虑一种情况，我们只对字符串进行了一次插入或删除操作，其实是没有必要进行diff，我们通过匹配两个字符串的通用前缀/后缀，可以直接找出差异，即便字符串进行了多次插入删除，查找公共前后缀也能在一定程度上减小diff的计算量，因为使用二分搜索的时间复杂度是O(nlogn)。

例如下面的两个字符串的公共前缀为abc，后缀为g，可以直接去掉。
```text
A: -> [a b c d e f g] <-
B: -> [a b c h f e g] <-
```

#### 寻找最小的移动次数

diff 算法首先需要一个旧数组的索引`{1:0,2:1,3:2,4:3}`，通过该索引计算出新数组的对应位置。然后计算数组索引的最长上升子序列，通常使用动态规划实现，具体不做展开。
```text
A: [a b c d]
B: [d a b c]
P: [3 0 1 2]
LIS: [0 1 2]
```

然后我们自右向左遍历新数组和 LIS 序列，查看元素的位置是否能与 LIS 序列的任何一个值匹配，若匹配则位置无需移动，然后取前一个LIS值继续，否则将其移动到当前遍历新数组元素的前面。
如上面的例子中，中有遍历到第一位时才不匹配，所以将B[1]=d移动到A[1]=a的前面即可。

为什么LIS能够做到这一点，因为它在整体上保留了数组升序排列的相对位置。

!!!
<iframe src="https://codesandbox.io/embed/children-diff-comparaison-umfqp?fontsize=14&view=preview" title="Children diff comparaison" allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>
!!!

参考文章：
https://www.zhihu.com/question/65824137
https://github.com/NervJS/nerv/issues/3


  [1]: https://img.zhangchen915.com/2019/10/1530262631.jpg
