---
title: " next_permutation 算法原理和JS实现"
categories: ["算法"]
tags: []
draft: false
slug: "446"
date: "2017-09-19 13:39:00"
---

C++ STL中的 next_permutation 函数和 prev_permutation 两个函数提供了对于一个特定排列P，求出其后一个排列P+1和前一个排列P-1的功能。

我们假设一个序列Pn=<3 6 4 2>，我们可以从右起第一位看成各位，第二位为十位以此类推，那么很容易得出递减序列表示的数最大，这个例子中<6 4 2>为递减，已经无法通过交换数字位置得到更大的数，所以我们找到第一个非递减元素3，然后从前面递减序列中找到第一个大于它的元素4，交换着两个元素得到<4 6 3 2>，但是这里<6 3 2>仍是递减，而我们需要以4开头的最小序列，所以我们还要将递减序列颠倒成递增序列，最终得到Pn+1=<4 2 3 6>。

算法步骤如下：

- 从尾端向前，找到两个相邻元素  *i < *(i+1)
- 再从尾端向前，找到第一个元素满足 *j > *i
- swap(i, j), 然后把 i+1 之后的所有元素 reverse 倒排

```c
#include<cstdio>
#include<cstring>

void inline swap(char *s1,char *s2){
    char t=*s1;*s1=*s2;*s2=t;
}

//反转字符串函数,s,e分别执行字符串的开始和结尾
void reverse(char *s,char *e){
    for(e--;s<e;s++,e--)swap(s,e);
}

bool next_permutation(char *start,char *end){
    char *cur = end-1, *pre=cur-1;
    while(cur>start && *pre>=*cur)cur--,pre--;
    if(cur<=start)return false;
    
    for(cur=end-1;*cur<=*pre;cur--);//找到逆序中大于*pre的元素的最小元素 
    swap(cur,pre);
    reverse(pre+1,end);//将尾部的逆序变成正序 
    return true;
}
```

JS实现：

```js
function next_permutation( arr ) {
    const length = arr.length;
    if( length <=1 ) return false;
    
    let i = length;
    while(true){
        var t = i;
        i--;
        if( arr[ i ] < arr[ t ] ) {
            var j = length;
            while( !( arr[ i ] < arr[ --j ] ) );

            const temp = arr[ i ];
            arr[ i ] = arr[ j ];
            arr[ j ] = temp;
            
            // splice()函数做了两件事，首先将arr的t以后的元素移除，并且将移除的部分颠倒后保存在rev中
            var rev = arr.splice( t, length ).reverse();

            // 将后面颠倒后的部分连接至arr，这里不能用concat()来连接，否则改变的就不是原来的arr了
            Array.prototype.push.apply( arr, rev );
            return true;
        }
        if(!i){
            arr.reverse();
            return false;
        }
    }
}
```
