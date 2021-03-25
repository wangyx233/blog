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
> 函数组件/无状态组件，在没有Hooks加持的条件加，函数组件无法维护自己的state

二者并没有优劣，只是根据不同的场景采用不同的形式。
> 区别：函数组件会捕获render内部的状态。面向对象与函数式的不同。this的区别
对于函数组件，props会保持渲染结果符合预期。更利于逻辑拆分与重用组件
为了回归 UI = render(data)的本质，所以需要补齐函数组件所缺乏的的类组件的能力，React Hooks应运而生。即一套能够使函数组件更强大、更灵活的“钩子”
ex.

- useState() 为函数组件引入了state，const [state, setState] = useState(initialState)
- useEffect() 允许函数执行副作用操作，即弥补了生命周期的缺失，比如componentDidMount、componentDidUpdate和componentWillUnmount。操作DOM、订阅事件、调用外部API。
useEffect(callBack, [])
- callback 是否有return,决定是否在卸载阶段执行。有return则会在挂载执行return之前的内容，卸载阶段执行return的函数。即useEffect回调中返回了一个函数，那它会被作为清除函数处理。
- [],判断是否每次页面渲染后都执行。如果 []不为空，React会在每次渲染前对比前后两次的渲染，查看数组内部是否有变量发生了更新。

## 所以 Why?
- 告别难以理解的Class，this和生命周期，降低学习成本。
- 更好的进行逻辑拆分，避免重复冗余的代码。
- 状态复用 HOC（高阶组件）
- 函数补齐能力还需继续完善，声明周期还有未纳入Hooks中的

## 原则

1. 只在React函数中调用Hook
2. 不要在循环，条件和嵌套中用Hook。why？

以setState为例，
- mountState，初始化hook，将hook相关的所有信息都收敛在一个hook对象里，hook对象之间以单向链表的形式相互串联。
- updateState，按顺序去遍历之前构造好的链表，然后取相应的数据进行渲染。重点：按“顺序”去修改！


