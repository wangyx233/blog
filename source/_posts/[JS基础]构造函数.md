---
title: 【JS基础】构造函数
categories: ["JavaScript"]
toc: true
---

### what？

> 当然是用来构造对象的函数啦~ 当然可以简单的这样理解

构造函数（constructor），是专门用来生成实例对象的函数，描述实例对象的基本结构。为了与普通函数区别，构造函数名称的第一个字母通常大写，有自己的特征与用法。

- 特点：
  1. 函数内部使用`this`关键字，代表要生成的对象实例
  2. 生成对象的时候，要使用`new`命令

等等 `new` 是什么?

```
const Person = function (name) {
  this.name = name
}

const Amanda = new Person('Amanda')
```

> new 命令的作用其实是，执行构造函数，返回一个实例对象。使用`new`时，构造函数也可以接受参数。

如果不使用`new`，直接调用构造函数会怎样？

> 那构造函数只能像普通函数一样执行，不会返回实例对象，函数的默认返回是 undefined 呀。这时候`this`指向变成另一个话题啦

如何保证构造函数必须与`new` 一起用呢？
如果没用`new`，则报错，或者直接返回一个实例对象

1. 构造函数内使用严格模式，`use strict`。这样的话，`this`不能指向全局，而是 undefined
2. 判断`this`不是实例，`return new Fun()`

> 这一顿操作`new`到底做了什么呢？

1. 创建一个空对象，作为要返回的对象实例
2. 将这个空对象的原型`__proto__`，指向构造函数的`prototype`
3. 构造函数的`this`指向改为这个空对象(即这个空对象赋值给函数内部的`this`关键字)
4. 构造函数执行返回

> 因此，构造函数之所以成为构造函数，是说这个函数的功能，是操作一个空对象，通过`this`，将其构造成需要的样子。

如果构造函数内部有`return`，需要注意：

- 如果`return`返回一个对象，则返回的实例对象是，该对象
- 如果不是，则忽略`return`的内容，返回`this`对象

### 总结

实现一个`new`吧

```
var myNew = (constructor, ...args) => {
  const obj = {}
  obj.__proto__ = constructor.prototype //  Object.setPrototypeOf(obj, constructor.prototype)
  const result = constructor.apply(obj, args)
  return (typeof result === 'object' && result !== null) ? result : obj
}
```

> Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的`__proto__`

```
var myNew = (constructor, ...args) => {
  const obj = Object.create(constructor.prototype)
  const result = constructor.apply(obj, args)
  return (typeof result === 'object' && result !== null) ? result : obj
}
```
