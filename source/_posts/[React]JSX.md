---
title: 【React】Why JSX?
categories: ["React"]
tags: ["React"]
toc: true
---

# Question

- JSX 的本质是什么，它和 JS 之间到底是什么关系？
- 为什么要用 JSX？不用会有什么后果？
- JSX 背后的功能模块是什么，这个功能模块都做了哪些事情？

<!--more-->

# What？

> JSX 是 JavaScript 的一个语法扩展，实质是快速构建页面结构的语法糖
> ReactDOM 在渲染所有输入内容之前，默认会进行转义，可以有效地防止 XSS 攻击

```
React.creatElement(type, config, children)  // 格式化数据，调用ReactElement创建虚拟DOM

export function createElement(type, config, children) {
  ....
  return ReactElement(
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current,
    props,
  );
}

const ReactElement = function(type, key, ref, self, source, owner, props) {
  const element = {
    // REACT_ELEMENT_TYPE是一个常量，用来标识该对象是一个ReactElement
    $$typeof: REACT_ELEMENT_TYPE,

    // 内置属性赋值
    type: type,
    key: key,
    ref: ref,
    props: props,

    // 记录创造该元素的组件
    _owner: owner,
  };
  return element;
}

ReactDOM.render(element, container, [callback]) // 真实的DOM
```

# Why？

代码变得更为简洁，而且代码结构层次更为清晰
JSX 层次分明，嵌套逻辑清晰。用 JSX 降低学习成本，提高研发效率和研发体验

# How？

是什么 -> 不用会怎样 -> 用了有什么好处
JSX 是一个 JavaScript 的语法扩展。主要用于声明 React 元素，通过 Babel 插件编译为 React.createElement。所以 JSX 更像是 React.createElement 的一种语法糖。
Babel 读取代码并解析，生成 AST，再将 AST 传入插件层进行转换，在转换时就可以将 JSX 的结构转换为 React.createElement 的函数
