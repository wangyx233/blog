---
title: 【React】事件系统
categories: ["React"]
tags: ["React"]
toc: true
---
# DOM事件流
1. 事件捕获
2. 目标阶段
3. 事件冒泡

# 事件委托/事件代理
> 把多个子元素的同一类型的监听逻辑，合并到父元素上通过一个监听函数来管理的行为，就是事件委托。
e.target 这个属性，指的是触发事件的具体目标，记录着事件的源头。

# React事件系统

## 合成事件
> 当事件在具体的DOM节点上被触发后，最终都会冒泡到document(或者ReactDOM.render绑定的元素)，document上所绑定的统一事件处理程序会将事件分发给具体的组件实例。

合成事件是React自定义的事件对象，符合W3C规范，在底层抹平了不同浏览器的差异，在上层向开发者暴露统一的，稳定的，与DOM原生事件相同的事件接口。不必关注兼容问题，专注业务开发。
合成事件不是原生的事件，但是它保存了原生事件的引用，e.nativeEvent
> React 事件的命名采用小驼峰式（camelCase），而不是纯小写。使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。不能通过返回 false 的方式阻止默认行为。你必须显式的使用 preventDefault
比如：
```
handleClick() {
  console.log('this is:', this);
}
<div onClick={()=>this.handleClick()}></div>
```
