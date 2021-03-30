---
title: 【React】Virtual DOM
categories: ["React"]
tags: ["React"]
toc: true
---

# What？

Virtual DOM本质上是JS 和 DOM 之间的一个映射缓存，是能够描述DOM结构及其属性的JS对象。

- 虚拟DOM是JS对象
- 虚拟DOM是对真实DOM的描述

挂载阶段，根据JSX的描述，构建出虚拟DOM，然后通过ReactDOM.render实现虚拟DOM到真实DOM的映射
更新阶段，页面的变化在作用于真实DOM之前，会先作用于虚拟DOM

# Why？

- jQuery解决DOM API 不好使的问题。还解决浏览器兼容，链式API调用，插件扩展
- 模板引擎，只关心数据，不关心DOM细节。操作真实DOM的范围过大、频率过高，但思想是数据驱动视图
- 虚拟DOM的点不在性能。虚拟DOM有没有借鉴模板引擎无法考证，但是思想上有一定的递进关系。JSX不是模板，而是JS的一个体验像模板的扩展语法糖，然后增加了个虚拟DOM的缓存层。

> 差量更新。即当DOM操作比较频繁时，会将前后两次的DOM树变化进行对比diff，定位到需要更新的部分，生成一个“补丁集”，把“补丁”patch打在需要更新的那部分真实DOM上，就可以实现差量更新
> 批量更新。batch函数处理，把短时间内的多次DOM操作合成一个次更新，即把收集的多个补丁集暂存到队列中，再讲最终结果交给渲染函数，实现DOM的集中化批量更新。

1. 研发体验/研发效率。虚拟DOM思想是数据驱动视图，前端声明式编程。
2. 性能不是卖点。性能还不错，性能量化不能一概而论只看一个指标，得看实际情况。DOM操作量级有关
3. 价值？跨平台，一次编码多次应用。虚拟DOM是对实际渲染内容的一次抽象
4. 规避xss攻击

缺点：
1. 内存占用高，虚拟DOM，object
2. 无法进行极致优化。如Google Earth

# How?
## 调和Reconciliation 和 Diff算法
> 调和是将虚拟DOM映射到真实DOM的过程。Diff算法只是其中的一个小部分，深度优先遍历

1. React 15为代表的“栈调和”
- 树对比
- 组件对比
- 元素对比
2. React 16以来的“Fiber调和”

# Better？

1. 避免跨层级节点移动
2. 设置唯一的key，尽量减少组件层级
3. 设置shouldComponentUpdate 或者 React.pureComponet 减少 diff 次数

