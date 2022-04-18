---
title: 【复盘】学了又好像没学过
categories: ["JavaScript"]
toc: true
---

> 基础不扎实啊，学了，又好像没学过 😭

### THIS 指向

> `函数调用`时，根据`执行上下文`确定的。

1. 简单调用，严格模式是 undefined，否则绑定在全局对象 window/globa。l。
2. 显示调用 apply/call/bind，绑定到指定的参数对象上。
3. 对象调用，绑定在该对象上
   在执行函数时，如果函数中的 this 是被上一级的对象所调用，那么 this 指向的就是上一级的对象；否则指向全局环境。
4. 构造函数 new 调用，绑定在新创建的对象上。
   如果构造函数中显式返回一个值，且返回的是一个对象，那么 this 就指向这个返回的对象；如果返回的不是一个对象，那么 this 仍然指向实例{}。
5. 箭头函数的 this，由外层（全局/函数）的上下文绑定的 this 决定。
   箭头函数的绑定无法被修改，this 始终沿着作用域链向上找（定义时决定，而不是执行时决定）。

说明：

- 通过 call、apply、bind、new 对 this 绑定的情况称为显式绑定。
- 根据调用关系确定的 this 指向称为隐式绑定。
- call、apply 的显式绑定一般来说优先级更高。
- new 绑定的优先级比显式 bind 绑定更高。
- const 声明的变量不会挂载到 window 全局对象当中，var 会。

QA: 严格模式都有哪些情况？

- `use strict`
- `类表达式、类声明`中是强制严格模式的!!!

```
window.name = 'hello'
function A() {
  this.name = 123
}
A.prototype.getName = function () {
  console.log(this)
  return this.name + 1
}
var a = new A()
var funcA = a.getName
funcA() // 'hello1'
a.getName() // 124

window.name = 'hello'
class A {
  constructor() {
    this.name = 123
  }
  getName = function() { // Uncaught TypeError: Cannot read properties of undefined
    console.log(this)
    return this.name + 1
  }
  getName = () => { // 124
    console.log(this)
    return this.name + 1
  }
}
var a = new A()
var funcA = a.getName
funcA()
```

如果要改变`this`指向有什么方法呢？

- 首先想到用`call`,`apply`,`bind`来修改
- 借助对象调用
- 箭头函数

> 实现一个 bind 方法

- 返回一个函数，this

```
Function.prototype.bind = Function.prototype.bind || function (context, ...args) {
    if (typeof this !== "function") throw new TypeError('need a function')
    const fn = this;
    return function (...args2) {
        return fn.apply(context, [...args, ...args2])
    }
}
```

注：

- 边界情况
- bind 返回的函数如果作为构造函数，搭配 new 关键字出现的话，我们的绑定 this 就需要“被忽略”
- apply 可以改为对象的隐式调用
