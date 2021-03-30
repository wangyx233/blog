---
title: 【React】setState 同步 or 异步？
categories: ["React"]
tags: ["React"]
toc: true
---

# 异步？
为什么要异步？
- 避免重复的re-render
- 批量更新，setState会在一个队列中，相同的会做合并，只针对最新的state做一次更新
减小频繁re-render

在React自己的生命周期和合成事件中，可以拿到控制权，将状态放入队列，控制执行节奏。即isBatchingUpdates -> true，进入dirtyComponents

# 同步？

在React管控下的setState一定是异步的，发生了同步的行为，是有其他操作帮助setState脱离了React的管控。
在 addEventListener 、setTimeout、setInterval 会同步更新

# Why？
React 15为例，
1. 批量更新 - batchingStrategy(锁管理器，锁：isBatchingUpdates)
2. 事务 Transaction 机制
3. 保持内部一致性，props的更新（props的更新来自于父组件state的改变）
4. 支持异步渲染，启用并发更新

> PS: React 17之前是通过isBatchingUpdates判断，之后是lanes算法的优先级来区分

# Q&A
1. 合成事件 -> 事件委托
 React 17 版本之前，事件监听是在document上，DOM事件触发后冒泡到document，React找到对应的组件，造出一个合成事件，并按组件数模拟一遍事件冒泡。
 React 17 版本，事件委托不在挂载document上，而是挂在DOM容器上，即ReactDom.Render调用的节点。

2. 看看输出什么？
```
class Test extends React.Component {
  state  = {
      count: 0
  };
  componentDidMount() {
    this.setState({count: this.state.count + 1});
    console.log(this.state.count); // 0
    this.setState({count: this.state.count + 1});
    console.log(this.state.count); // 0
    setTimeout(() => {
      this.setState({count: this.state.count + 1});
      console.log(this.state.count); // 2
      this.setState({count: this.state.count + 1});
      console.log(this.state.count); // 3
    }, 0);
  }
  render() {
    return null;
  }
};
```