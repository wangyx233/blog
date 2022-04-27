---
title: 【复盘】虚拟DOM转换
categories: ["JavaScript"]
toc: true
---

实现一个`render`函数，完成虚拟 DOM 到真实 DOM 的转换

```
let domNodes = {
  tagName: 'ul',
  props: {
    class: 'list'
  },
  children: [{
    tagName: 'li',
    children: ['apple']
  }, {
    tagName: 'li',
    children: ['orange']
  }]
}

<ul class='list'>
  <li>apple</li>
  <li>orange</li>
</ul>

```

```
function render(element) {
  const {
    tagName,
    props = {},
    children = []
  } = element
  const node = document.createElement(tagName)
  Object.keys(props).forEach(attr => {
    node.setAttribute(attr, props[attr])
  })
  children.forEach(child => {
    if (typeof child === 'object') {
      const childNode = render(child)
      node.append(childNode)
    } else {
      node.append(document.createTextNode(child))
    }
  })
  return node
}

let dom = render(domNodes)
```

> 其实冷静下来，很容易写出来的 😭

<!--more-->

基础回顾

```
document.createElement()
document.createTextNode()
.setAttribute()
.append()
```
