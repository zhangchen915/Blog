---
title: "JS 实现LRU缓存"
categories: ["前端","算法"]
tags: []
draft: false
slug: "608"
date: "2019-07-02 23:19:00"
---

为什么不直接用对象这种k-v结构，因为内存空间很宝贵而且是有限的。
在浏览器我们无所谓，但服务端就不一样了，例如nuxt就是用了LRU算法缓存接口数据或者渲染后的dom对象。

`LRU`（Least Recently Used）把最近没用过的一个置换出去，意思就是如果最近用到了就放到前面，如果不够了最后一个就是最近没使用的，将其删除掉就好。使用双链表就能满足这种需求。

```
/*
 * Illustration of the design:
 *
 *       entry             entry             entry             entry
 *       ______            ______            ______            ______
 *      | tail |.newer => |      |.newer => |      |.newer => | head |
 *      |  A   |          |  B   |          |  C   |          |  D   |
 *      |______| <= older.|______| <= older.|______| <= older.|______|
 *
 */

```

```js
class Node {
    prev = null;
    next = null;
    constructor(k, v) {
        if (valid(k)) this.key = k;
        if (valid(v)) this.value = v;
    }

    valid(e) {
        return typeof key != "undefined" && key !== null
    }
}

class LRU {
    size = 0;
    head = null;
    tail = null;
    map = {};
    constructor(limit = 1000) {
        this.limit = limit
    }

    setHead(node) {
        node.next = this.head;
        node.prev = null;
        if (this.head !== null) this.head.prev = node;
        if (this.tail === null) this.tail = node;
        this.size++;
        this.map[node.key] = node;
    }

    set(k, v) {
        const node = new Node(k,v);
        if (this.map[key]) {
            this.map[key].value = node.value;
            this.remove(node.key);
        } else {
            if (this.size >= this.limit) {
                delete this.map[this.tail.key];
                this.size--;
                this.tail = this.tail.prev;
                this.tail.next = null;
            }
        }
        this.setHead(node);
    }

    get(k) {
        if (this.map[k]) {
            const value = this.map[k].value;
            const node = new Node(k, v);
            this.remove(k);
            this.setHead(node);
            return value;
        } else {
            console.log("Key " + k + " does not exist in the cache.")
        }
    }

    remove(key) {
        const node = this.map[key];
        if (node.prev !== null) {
            node.prev.next = node.next;
        } else {
            this.head = node.next;
        }
        if (node.next !== null) {
            node.next.prev = node.prev;
        } else {
            this.tail = node.prev;
        }
        delete this.map[key];
        this.size--;
    }
}
```

插入删除时间复杂度都是`O(1)`。

现在为止我们实现了最为核心部分，但是缓存也不能无限的停留在内存中，像接口这些数据有时会发生改变，所以还需要为缓存加上有效期，在get时判断是否过期。

LRU能带给我们一定的收益，但是他也让我们的程序拥有了状态，也就不能做分布式来多实例负载均衡。
