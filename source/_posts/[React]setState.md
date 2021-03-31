---
title: 【React】setState 同步 or 异步？
categories: ["React"]
tags: ["React"]
toc: true
---

> 在React管控下的setState是异步，而脱离React管控的则表现为同步。
<!--more-->
# 异步？
在React自己的生命周期和合成事件中，可以拿到控制权，将状态放入队列，控制执行节奏。即isBatchingUpdates -> true，进入dirtyComponents，false则循环更新dirtyComponents里的所有组件。
```
// 函数分发
ReactComponent.prototype.setState = function (partialState, callback) {
  this.updater.enqueueSetState(this, partialState);
  if (callback) {
    this.updater.enqueueCallback(this, callback, 'setState');
  }
};

// 将新的state放到组件的状态队列中，并且用enqueueUpdate来处理要更新的实例
enqueueSetState: function (publicInstance, partialState) {
  // 根据 this 拿到对应的组件实例
  var internalInstance = getInternalInstanceReadyForUpdate(publicInstance, 'setState');
  // 这个 queue 对应的就是一个组件实例的 state 数组
  var queue = internalInstance._pendingStateQueue || (internalInstance._pendingStateQueue = []);
  queue.push(partialState);
  //  enqueueUpdate 用来处理当前的组件实例
  enqueueUpdate(internalInstance);
}

// batchingStrategy用于批量更新的对象
function enqueueUpdate(component) {
  ensureInjected();
  // 注意这一句是问题的关键，isBatchingUpdates标识着当前是否处于批量创建/更新组件的阶段
  if (!batchingStrategy.isBatchingUpdates) {
    // 若当前没有处于批量创建/更新组件的阶段，则立即更新组件
    batchingStrategy.batchedUpdates(enqueueUpdate, component);
    return;
  }
  // 否则，先把组件塞入 dirtyComponents 队列里，让它“再等等”
  dirtyComponents.push(component);
  if (component._updateBatchNumber == null) {
    component._updateBatchNumber = updateBatchNumber + 1;
  }
}

var ReactDefaultBatchingStrategy = {
  // 全局唯一的锁标识
  isBatchingUpdates: false, // 当前未进行任何批量操作
  // 发起更新动作的方法
  batchedUpdates: function(callback, a, b, c, d, e) {
    // 缓存锁变量
    var alreadyBatchingStrategy = ReactDefaultBatchingStrategy.isBatchingUpdates
    // 把锁“锁上”
    ReactDefaultBatchingStrategy.isBatchingUpdates = true // 现在正处于批量操作中，需要更新的组件暂时进入DirtyComp
    if (alreadyBatchingStrategy) {
      callback(a, b, c, d, e)
    } else {
      // 启动事务，将 callback 放进事务里执行
      transaction.perform(callback, null, a, b, c, d, e)
    }
  }
}

// 事务通过wrapper进行封装。一个wrapper包含一对initialize和close方法
var RESET_BATCHED_UPDATES = {
  initialize: emptyFunction,
  close: function () {
    ReactDefaultBatchingStrategy.isBatchingUpdates = false;
  }
};
// FLUSH_BATCHED_UPDATES在close阶段，会循环遍历所有的dirtyComponents，调用updateComponent刷新组件
var FLUSH_BATCHED_UPDATES = {
  initialize: emptyFunction,
  close: ReactUpdates.flushBatchedUpdates.bind(ReactUpdates)
};
var TRANSACTION_WRAPPERS = [FLUSH_BATCHED_UPDATES, RESET_BATCHED_UPDATES];

```

为什么要异步？
- 避免重复的re-render
- 批量更新，setState会在一个队列中，相同的会做合并，只针对最新的state做一次更新
减小频繁re-render

# 同步？

在React管控下的setState一定是异步的，发生了同步的行为，是有其他操作帮助setState脱离了React的管控。
在 addEventListener 、setTimeout、setInterval 会同步更新

# Why？
React 15为例，
1. 批量更新 - batchingStrategy(锁管理器，锁：isBatchingUpdates)
2. 事务 Transaction 机制，Transaction创建一个黑盒，该黑盒能封装任何的方法。
3. 保持内部一致性，props的更新（props的更新来自于父组件state的改变）
4. 支持异步渲染，启用并发更新

> PS: React 17之前是通过isBatchingUpdates判断，之后是lanes 算法的优先级来区分

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