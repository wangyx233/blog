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
> JSX是JavaScript的一个语言扩展，实质是快速构建页面结构的语法糖
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
JSX层次分明，嵌套逻辑清晰。用JSX降低学习成本，提高研发效率和研发体验

# How？