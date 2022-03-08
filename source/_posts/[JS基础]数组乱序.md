---
title: 【JS基础】数组乱序
categories: ["JavaScript"]
toc: true
---

最近在解决一个分布式高可用的问题，如何能够让多机的定时任务能够不重复执行，引入了 redis 的分布式锁。但一个任务列表如果都顺序遍历那每个任务的抢占可能不会那么随机，所以想让任务列表打乱顺序，就稍微了解了一下数组乱序的实现。

> （其实最后用的还是 lodash 的\_.shuffle() 😯

一、随机数？

```
[12,4,16,3].sort(function() {
    return .5 - Math.random();
})
```

v8 在处理 sort 方法时，使用了插入排序和快排两种方案。当目标数组长度小于 10（不同版本有差别）时，使用插入排序；反之，使用快排。

二、Fisher–Yates shuffle 洗牌算法

```
Array.prototype.shuffle = function() {
    let array = this;
    let len = array.length,
        temp, i;
    while (len) {
        i = Math.floor(Math.random() * len--);
        temp = array[len];
        array[len] = array[i];
        array[i] = temp;
    }
    return array;
}
```
