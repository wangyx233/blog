---
title: 【读书】JavaScript高级程序设计
categories: ['生活']
tags: ['JavaScript']
toc: true
---

script标签
defer vs async
共同点：只对外部脚本有效，src='xxx'
不同：
> defer表明脚本立即下载，但是执行会推迟，在DOMContentLoaded之前执行。即加载无需等待，HTML5规定执行需要有序的。
> async表明脚本会并行下载，下载完立即执行，不保证执行的顺序。会在页面loaded执行前完成。类似 DOM 内通过createElement创建script标签(可配合rel='preload')。

