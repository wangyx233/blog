---
title: 【WebAPI】DOM
categories: ['JavaScript']
toc: true
---

> DOM -> Document Object Model

- DOM数据结构
- DOM常用的API
- attr和property的区别
- 如何一次插入多个DOM节点

# 知识点
- DOM 本质
- DOM 节点操作
- DOM 结构操作
- DOM 性能

1. DOM本质：从HTML解析出来的一棵树
2. DOM节点
   - 节点获取的常见API
   - 通过js修改页面渲染结构：节点property，直接通过属性修改，如：dom.style.width = xxxx,dom.className = 'xxx'
   - 修改的是标签的属性：节点attribute，通过getAttibute('style', 'font-size:16px'),setAttribute()
   > property： 修改JS对象属性，不会体现在html中；attribute：修改html属性，改变html结构。都有可能引起DOM重新渲染，可以先优先用property
3. DOM结构
    - 新增/插入节点，createElement()，appendChild()未存在的节点直接创建，已存在的移动。
    - 删除节点：removeChild()
4. DOM性能
    - 避免频繁的DOM操作
    - 对DOM查询做缓存，get操作用变量缓存一下
    - 将频繁操作改为一次操作，通过document.createDocumentFragment() 创建一个文档片段，将高频操作插入到文档片段中，最后再把文档片段插入






