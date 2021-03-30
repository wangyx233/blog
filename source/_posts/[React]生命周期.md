---
title: 【React】生命周期
categories: ["React"]
tags: ["React"]
toc: true
---

## React 15

{% img /gallery/react15life.png %}

- Mounting 阶段：组件的初始化渲染
  在组件一生中仅会发生一次。组件初始化，然后被渲染到真实的 DOM 中，完成“首次渲染”。render 不会操作真实的 DOM，真实 DOM 的渲染工作，在挂载阶段是由 ReactDOM.render 来承接的。componentDidMount 在渲染后触发，此时真实的 DOM 已经挂载，可以进行数据请求，初始化等操作。
- Updating 阶段：组件的更新
  1. 父组件更新触发的更新。componentReceiveProps 并不是由 props 的变化触发的，而是由父组件的更新触发的
  2. 组件调用 setState 触发更新。 shouldComponentUpdate 决定组件的 re-render
- Unmounting 阶段：组件的卸载。 组件被移除了，或者设置了 key，在 render 过程中发现 key 改变了

## React 16

### React 16.4

{% img /gallery/react16life.png %}

<!--more-->

- Mounting 阶段：组件的初始化渲染
  废弃了 componentWillMount，新增了 getDerivedStateFromProps。getDerivedStateFromProps 不是 componentWillMount 的替代品，而是试图替换掉 componentWillReceiveProps，因此它有且仅有一个用途：使用 props 来派生/更新 state。

  ```
  static getDerivedStateFromProps(props, state)
  // 1. getDerivedStateFromProps 是一个静态方法。静态方法不依赖组件实例而存在，因此在这个方法内部是访问不到 this
  // 2. 该方法可以接收两个参数：props 和 state，它们分别代表当前组件接收到的来自父组件的 props 和当前组件自身的 state
  // 3. getDerivedStateFromProps 需要一个对象格式的返回值，getDerivedStateFromProps 的返回值之所以不可或缺，是因为 React 需要用这个返回值来更新（派生）组件的 state，getDerivedStateFromProps 方法对 state 的更新动作并非“覆盖”式的更新，而是针对某个属性的定向更新
  ```

  React 16 之前，render 方法必须返回单个元素，而 React 16 允许我们返回元素数组和字符串。
  componentDidCatch(error, info)

- Updating 阶段：组件的更新
  在 React 16.4 中，任何因素触发的组件更新流程（包括由 this.setState 和 forceUpdate 触发的更新流程）都会触发 getDerivedStateFromProps；而在 v 16.3 版本时，只有父组件的更新会触发该生命周期。
  在确保生命周期函数的行为更加可控可预测，从根源上帮开发者避免不合理的编程方式，避免生命周期的滥用；同时，也是在为新的 Fiber 架构铺路。

  ```
  getSnapshotBeforeUpdate(prevProps, prevState) {}
  ```

  getSnapshotBeforeUpdate 的返回值会作为第三个参数给到 componentDidUpdate。它的执行时机是在 render 方法之后，真实 DOM 更新之前。在这个阶段里，我们可以同时获取到更新前的真实 DOM 和更新前后的 state&props 信息。getSnapshotBeforeUpdate 要想发挥作用，离不开 componentDidUpdate 的配合。

- Unmounting 阶段：组件的卸载，同 React 15

## 总结

为什么要改呢？
React 16 改造生命周期的主要动机是为了配合 Fiber 架构带来的异步渲染机制，针对生命周期中长期被滥用的部分推行了具有强制性的最佳实践。这一系列的工作做下来，首先是确保了 Fiber 机制下数据和视图的安全性，同时也确保了生命周期方法的行为更加纯粹、可控、可预测。Fiber 会使原本同步的渲染过程变成异步的。
新的生命周期分为Render 和 Commit阶段，Commit阶段涉及实际的DOM修改，不会被打断，而Render阶段可以被打断
如果在废除的 willMount ，willUpdate，willReceiveProps生命周期中进行如下操作，由于这些生命周期都在Render阶段，可随时被打断。配合Fiber的异步渲染可能会导致无法预料的结果展示。
 - setState
 - fetch
 - 操作真实DOM
  ......

> https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html
