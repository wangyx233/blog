---
title: 【React】ReactDom.render
categories: ["React"]
tags: ["React"]
toc: true
---

# ReactDom.render 调用栈逻辑

- scheduleUpdateOnFiber
- commitRoot 正式渲染DOM

1. 初始化阶段 - 完成Fiber🌲基本实体的构建
2. render阶段 - 深度优先搜索过程
3. commit阶段
