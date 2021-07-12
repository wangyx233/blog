---
title: 【React】源码从入门到跑路
categories: ["React"]
tags: ["React"]
toc: true
---
- event 事件为什么需要bind(this)
1. 对于class内普通定义的触发函数需要bind this，而使用静态方法定义则可以不用绑定
```
class Test {
    constructor(props) {
        this.normalFun = this.normalFun.bind(this) // 只执行一次
    }
    normalFun() {}
    staticFun = () => {}
    eventFun = event => {} // 追加的event参数
    render() {
        return <div onClick={this.staticFun}></div>
    }
}
``` 
2. 由于this指向的问题，如果普通模式不bind this的话，此时this指向为undefined，而静态方法的this指向当前实例
3. 如果要bind的话，bind this 的位置写在什么地方合适呢？和执行的次数有关，性能优化小点

- event 参数，不是原生的event，是组合事件
通过event._proto_.constructor区分，React的event是组合事件(SyntheticEvent)，原生的为MouseEvent。通过nativeEvent获取原生事件
event.target,event.currentTarget vs event.nativeEvent.target 触发的 event.nativeEvent.currentTarget 绑定的document，React 17之前。
自定义参数，最后会追加一个参数是event

区别 vue ：原生的事件，绑定在当前元素上
react：不是原生的事件，是组合事件SyntheticEvent模拟DOM事件所有能力，react 17 之前绑定在document，之后当前元素

- 表单
1. 受控组件，类似双向数据绑定
2. 非受控组件
propTypes 类型检查 component.propTypes
- 父组件子组件通讯
1. 传值 list = {xxx}
2. 传函数，父组件控制数据，数据提升 cFun = {xxx}

- setState
1. 不可变值
2. 可能是异步
3. 可能会被合并
4. 可能是同步
