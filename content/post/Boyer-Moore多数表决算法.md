---
title: "Boyer-Moore多数表决算法"
categories: ["算法"]
tags: []
draft: false
slug: "783"
date: "2020-02-23 00:14:00"
---

### 问题陈述
假设你有一个未排序的值列表。你想知道列表中是否存在该列表中一半以上元素的值。如果有则这个值是什么？尽可能高效地完成此任务。

出现此问题的一个常见原因可能是容错计算。执行多个冗余计算，然后验证大多数结果是否一致。‘

### 简单的解决方案

简单的解决方案是哈希表。时间复杂度是O(N)，空间复杂度是O(N)。

### Boyer-Moore多数表决算法

首先检查该count值。如果计数等于0，则将设置candidate为当前元素的值。接下来，首先将元素的值与当前candidate值进行比较。如果它们相同，则增加count1。如果它们不同，则减少count1。
```
candidate = 0
count = 0
for value in input:
  if count == 0:
    candidate = value
  if candidate == value:
    count += 1
  else:
    count -= 1

```

最终 candidate 即为众数，该算法空间复杂度是O(1)。

我们用下面一组数据来理解，我们把 5 看成 -1 ；0 看成 +1 ，只要不同就相互抵消，相同的元素相加，多的一方一定会把另一方抵消掉。下面是计算过程：

```
[5, 5, 0, 0, 0, 5, 0, 0, 5]

[1, 2, 1, 0, 1, 0, 1, 2, 1]  // count 取值

[5, 5, 5, 5, 0, 0, 0, 0, 0]  // candidate 取值

```

### 推广
每次删除三个不相同的数，最后留下的可能是出现次数超过`1/3`的数，这个思想可以推广到出现次数超过`1/k`次的元素有哪些。需要注意的是最后还需要再遍历一遍结果，判断次数最多和次多的是不是超过了`⌊ n/3 ⌋`次。

```c++
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int m = 0, n = 0, cm = 0, cn = 0;
        for (int i : nums) {
            if (m == i) {
                ++cm;
            } else if (n == i) {
                ++cn;
            } else if (cm == 0) {
                m = i;
                cm = 1;
            } else if (cn == 0) {
                n = i;
                cn = 1;
            } else {
                --cm;
                --cn;
            }
        }
        cm = cn = 0;
        for (int i : nums) {
            if (i == m)
                ++cm;
            else if (i == n)
                ++cn;
        }
        vector<int> res;
        const int N = nums.size();
        if (cm > N / 3)
            res.push_back(m);
        if (cn > N / 3)
            res.push_back(n);
        return res;
    }
};
```

#### 并行计算
[Finding the Majority Element in Parallel](http://www.crm.umontreal.ca/pub/Rapports/3300-3399/3302.pdf)

以 [1, 1, 1, 2, 1, 2, 1, 2, 2]  为例，我们可以将其拆开。

分组1：
[1, 1, 1, 2, 1]
candidate = 1
count = 3

分组2：
[2, 1, 2, 2]
candidate = 2
count = 2

可以将第1部分的结果视为与[1，1，1]相同，而将第2部分的结果视为与[2，2]。

### 参考文章:
https://gregable.com/2013/10/majority-vote-algorithm-find-majority.html
