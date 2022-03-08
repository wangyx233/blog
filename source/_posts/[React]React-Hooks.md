---
title: 【React】React-Hooks
categories: ["React"]
tags: ["React"]
toc: true
---

React-Hooks 自 React 16.8 以来才真正被推而广之

# What & Why

## 类组件 Class Component vs 函数/无状态组件

> 所谓类组件，就是基于 ES6 Class 这种写法，通过继承 React.Component 得来的 React 组件
> 函数组件/无状态组件，在没有 Hooks 加持的条件加，函数组件无法维护自己的 state

二者并没有优劣，只是根据不同的场景采用不同的形式。

> 区别：函数组件会捕获 render 内部的状态。面向对象与函数式的不同。this 调用的区别
> 注：对于函数组件，props 会保持渲染结果符合预期。更利于逻辑拆分与重用组件
> 为了回归 UI = render(data)的本质，所以需要补齐函数组件所缺乏的的类组件的能力，React Hooks 应运而生。即一套能够使函数组件更强大、更灵活的“钩子”
> ex.

- useState() 为函数组件引入了 state，const [state, setState] = useState(initialState)
- useEffect() 允许函数执行副作用操作，即弥补了生命周期的缺失，比如 componentDidMount、componentDidUpdate 和 componentWillUnmount。操作 DOM、订阅事件、调用外部 API。
  useEffect(callBack, [])，返回的函数成为“清除函数 ”
- callback 是否有 return,决定是否在卸载阶段执行。有 return 则会在挂载执行 return 之前的内容，卸载阶段执行 return 的函数。即 useEffect 回调中返回了一个函数，那它会被作为清除函数处理。
- [],判断是否每次页面渲染后都执行。如果 []不为空，React 会在每次渲染前对比前后两次的渲染，查看数组内部是否有变量发生了更新。注意引用类型的比较可能因为变化导致判断结果不同。
- useLayoutEffect() 同 useEffect 都是用来处理副作用的，底层函数签名也是一致的，都是 mountEffectImpl，在使用上也没有什么区别。但 useEffect 是异步调用的，useLayoutEffect 则是在 DOM 变更后同步调用，主要避免页面闪烁、调整样式、处理 DOM，但正是由于同步操作，所以避免在 useLayoutEffect 中做计算量大耗时的任务，以免出现阻塞。
  Hooks 开发需要抛弃生命周期的思考模式，以 effects 的角度重新思考

## 所以 Why?

优势：

- 告别难以理解的 Class，this 和生命周期，降低学习成本。不必用实践层面的约束解决设计层面的问题
- 解决业务逻辑难以拆分的问题。更好的进行逻辑拆分，避免重复冗余的代码。
- 使状态逻辑复用变得简单可行。状态复用 HOC（高阶组件）

局限：

- 函数补齐能力还需继续完善，声明周期还有未纳入 Hooks 中的
- 复杂的，耦合和自由。有严格的规则约束
- 常量声明

## 原则

1. 只在 React 函数中调用 Hook
2. 不要在循环，条件和嵌套中用 Hook。why？
   eslint-plugin-react-hooks

以 setState 为例，

- mountState，初始化 hook，将 hook 相关的所有信息都收敛在一个 hook 对象里，hook 对象之间以单向链表的形式相互串联。
- updateState，按顺序去遍历之前构造好的链表，然后取相应的数据进行渲染。重点：按“顺序”去修改！

PS:
心智模型：引用唐纳德·诺曼，是指用户头脑中对一个产品应该具有的概念和行为的知识。这个知识可能来源于用户以前使用类似产品的沉淀，或者用户根据使用该产品要达到的目标而对产品概念和行为的一种期望。

# Q&A: 如何设计 React 的组件？

> 展示组件的复用性更强，灵巧组件则更专注与业务本身

- 展示组件： 无状态组件，只做展示，独立运行

1. 代理组件
2. 样式组件
3. 布局组件

- 灵巧组件： 有状态组件，主要进行业务逻辑和数据状态处理

1. 容器组件
2. 高阶组件

- 逻辑复用
- 链式调用
- 渲染劫持
- 缺陷：丢失静态函数，refs 属性不能透传
