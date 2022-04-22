---
title: 【复盘】学了又好像没学过
categories: ["JavaScript"]
toc: true
---

> 基础不扎实啊，学了，又好像没学过 😭

### THIS 指向

> `函数调用`时，根据`执行上下文`确定的。

1. 简单调用，严格模式是 undefined，否则绑定在全局对象 window/global。
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

- `use strict`(只有 this 处于严格模式中，才会绑定为 undefined，
  与函数被调用的位置是否处于严格模式无关。)
- `类表达式、类声明`中是强制严格模式的!!!

其他：

- 赋值表达式的返回值是，赋的值
- Object.create(null) 创建的对象没有原型
- call/apply `null` 非严格模式下是`window`， 严格模式`null`

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
  getName() { // Uncaught TypeError: Cannot read properties of undefined
    console.log(this)
    return this.name + 1
  }
  getName = () => { // 124，等同于this.getName
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
Function.prototype.mybind = Function.prototype.bind || function (context, ...args) {
    if (typeof this !== "function") throw new TypeError('function called')
    const fn = this;
    const bound = function (...args2) {
        return fn.apply((this instanceof fn ? this : context), [...args, ...args2])
    }
    bound.prototype = new fn()
    return bound
}
```

注：

- 边界情况
- bind 返回的函数如果作为构造函数，搭配 new 关键字出现的话，我们的绑定 this 就需要“被忽略”
- apply 可以改为对象的隐式调用
- bind 函数也可以传参，有点 curry 的意思。所以，遇到`new`时，this 指向会改变，传参会在 bind 的基础上继续传入

### 原型相关

1. 引用类型都有隐式原型`__proto__`指向一个对象
2. 函数都有一个`prototype`，也是一个普通对象
3. 引用类型的`__proto__`都指向它的构造函数的`prototype`

简单理解： `实例.__proto__ -> 构造函数的prototype(的__proto__) -> 上一层的prototype`
构造函数的关注点在 prototype 上，它的 prototype 对象里有 constructor 指向寄己。自身的 proto 最终指向 Function.prototype。
实例的原型是看`__proto__`，构造函数的显示原型是`prototype`
对象的重点是 Object.prototype
函数的`__proto__` -> Function.prototype

`Class` 是构造函数的语法糖：

- 类的所有的方法都是定义在 prototype 上的
- 类内所有的方法都是不可枚举的，可以通过`getOwnPropertyNames`获取
  （Object.keys() -> for in hasOwnProperty）

获取 Array 的方法以及原型链上的方法

```
Object.getOwnPropertyNames(Array) // ['length', 'name', 'prototype', 'isArray', 'from', 'of']

Object.getOwnPropertyNames(Array.prototype) //['length', 'constructor', 'concat', 'copyWithin', 'fill', 'find', 'findIndex', 'lastIndexOf', 'pop', 'push', 'reverse', 'shift', 'unshift', 'slice', 'sort', 'splice', 'includes', 'indexOf', 'join', 'keys', 'entries', 'values', 'forEach', 'filter', 'flat', 'flatMap', 'map', 'every', 'some', 'reduce', 'reduceRight', 'toLocaleString', 'toString', 'at', 'findLast', 'findLastIndex']
```
