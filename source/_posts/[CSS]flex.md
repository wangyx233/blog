---
title: 【CSS】flex & grid
categories: ["css"]
toc: true
---

平时常用 flex 进行对齐和布局，习惯了 align-items 和 justify-content，也会遇到过宽度换行的情况。

flex: 1 是什么？ 2 是什么？ 10px？有单位是 flex-basis 的值，无单位的话则为 flex-grow 的值

> 即：一个无单位数(<number>): 它会被当作 flex:<number> 1 0;
> 注：省略值和默认值不同

```
/* 关键字值 */
flex: initial; // 0 1 auto 根据自身宽高设置尺寸，会收缩但不会伸长。适合一侧宽度固定，另一侧宽度任意，display: flex; 默认值
flex: auto; // 1 1 auto 根据自身宽高，会收缩会伸长；优先扩张，适合内容动态适配
flex: none; // 0 0 auto 根据自身宽高设置尺寸，不会收缩也不会伸长；适合自身不变的

flex-grow: 省略时1
flex-shrink: 省略时1
flex-basis: 省略时0
flex: 1 -> 1 1 0% // 优先缩小自己的体积，充分利用剩余空间，又不侵占其他元素的位置，适合等分
flex: 10px -> 1 1 10px
```

最近有使用 grid 布局

```
display: grid;
grid-template-columns: repeat(3, 1fr);
grid-template-rows: repeat(3, 1fr);
grid-gap: 10px;
```
