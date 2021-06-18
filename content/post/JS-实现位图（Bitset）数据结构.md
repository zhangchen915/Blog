---
title: "JS 实现位图（Bitset）数据结构"
categories: ["前端","数据结构"]
tags: []
draft: false
slug: "588"
date: "2018-08-21 18:16:00"
---

位图数组中每个元素在内存中占用1位，所以可以节省存储空间，在索引，数据压缩等方面有广泛应用。
我们使用类型化数组`Uint8Array`储存位图，数组中每一个元素占8个字节所以最大值是`255`。关键是寻找偏移量，`index>>3` 右移三位相当于除8。可以找到对应的数组元素，`1 << (index % 8)` 找到位对应数组元素的哪一个字节。最后通过按位判断该为1或0。

判断存在时：使用按位与`&`（只有两个操作数相应的比特位都是1时，结果才为1）
修改某项：使用按位或`|`

Typescript代码如下：
```ts
export class Bitmap {
    private bin: Uint8Array;

    constructor(size: number) {
        this.bin = new Uint8Array(new ArrayBuffer((size >> 3) + 1));
    }

    public get(index: number): boolean {
        const row = index >> 3;
        const bit = 1 << (index % 8);
        return (this.bin[row] & bit) > 0;
    }

    public set(index: number, bool: boolean = true) {
        const row = index >> 3;
        const bit = 1 << (index % 8);
        if (bool) {
            this.bin[row] |= bit;
        } else {
            this.bin[row] &= (255 ^ bit);
        }
    };

    public fill(num: number = 0) {
        this.bin.fill(num);
    }

    public flip(index: number) {
        const row = Math.floor(index / 8);
        const bit = 1 << (index % 8);
        this.bin[row] ^= bit;
    }
}
```
