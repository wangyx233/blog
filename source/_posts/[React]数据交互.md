---
title: 【React】数据交互
categories: ["React"]
tags: ["React"]
toc: true
---
React 的核心特征是“数据驱动视图”
```
UI = render(data)
```

## 基于 props 的单向数据流

> props：组件从概念上类似与JavaScript函数，它接受任意的入参(props)并返回用于描述页面展示内容的React元素。

所谓单向数据流，指的就是当前组件的 state 以 props 的形式流动时，只能流向组件树中比自己层级更低的组件。 比如在父-子组件这种嵌套关系中，只能由父组件传 props 给子组件，而不能反过来。

基于 props 传参这种形式，我们可以轻松实现父-子通信、子-父通信和兄弟组件通信。

<!--more-->

### 父-子组件通信
React 的数据流是单向的，父组件可以直接将 this.props 传入子组件，实现父-子间的通信。

### 子-父组件通信
考虑到 props 是单向的，子组件并不能直接将自己的数据塞给父组件，但 props 的形式也可以是多样的。假如父组件传递给子组件的是一个绑定了自身上下文的函数，那么子组件在调用该函数时，就可以将想要交给父组件的数据以函数入参的形式给出去，以此来间接地实现数据从子组件到父组件的流动。

```
class Child extends React.Component {
  state = {
    text: 'child's text'
  }

  changeText = () => {
    this.props.changeFatherText(this.state.text)
  }

  render() {
    return (
      <div className="child">
        <button onClick={this.changeText}>
          Change father's text
        </button>
      </div>
    );
  }
}

class Father extends React.Component {
  state = {
    text: "Init father's text"
  };
  changeText = (newText) => {
    this.setState({
      text: newText
    });
  };
  render() {
    return (
      <div className="father">
        <p>{`father's text：[${this.state.text}]`}</p>
        <Child
          changeFatherText={this.changeText}
        />
      </div>
    );
  }
```

### 兄弟组件通信
前提：兄弟组件之间共享了同一个父组件。这个前提条件使得我们可以继续利用父子组件这一层关系，将“兄弟 1 → 兄弟 2”之间的通信，转化为“兄弟 1 → 父组件”（子-父通信）、“父组件 → 兄弟 2”（父-子）通信两个步骤。

### 总结
props进行数据传递除了适合上述三种情况外，特别复杂的情况并不推荐， 比如多层嵌套的组件结构就不适用。会浪费很多中间代码，非常繁琐，中间组件也会引入很多无意义的属性，维护成本剧增，所以层层传递 props 要不得。

### “发布-订阅”模式驱动数据流
监听事件的位置和触发事件的位置是不受限的，所以如何实现呢？
发布-订阅模式中有两个关键的动作：事件的监听（订阅）和事件的触发（发布）：
- on()：负责注册事件的监听器，指定事件触发时的回调函数
- emit()：负责触发事件，可以通过传参使其在触发的时候携带数据
- off()：负责监听器的删除

```
class tryEventEmitter {
  constructor() {
    this.eventMap = {};
  }
  on(type, handler) {
    if (typeof handler !== 'function') throw new Error("请输入一个函数");
    if (!this.eventMap[type]) this.eventMap[type] = [];
    this.eventMap[type].push(handler);
  }
  emit(type, params) {
    if (this.eventMap[type]) {
      this.eventMap[type].forEach((handler, index) => {
        handler(params);
      });
    }
  }
  off(type, handler) {
    if (this.eventMap[type]) {
      this.eventMap[type].splice(this.eventMap[type].indexOf(handler) , 1);
    }
  }
}
```
