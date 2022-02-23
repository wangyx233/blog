---
title: 【JS基础】作用域和闭包
categories: ['JavaScript']
toc: true
---

# 思考
1. this的不同应用场景，以及取值
2. 手写bind函数
3. 闭包的场景


<!--more-->
# 知识点
1. 作用域，一个变量的合法使用范围
- 全局作用域
- 函数作用域
- 块级作用域（ES6）
> 自由变量，一个变量在当前作用域没有定义却被使用了，需要沿着作用域一层一层向上查找，找到为止，如果全局作用域都没有找到，那就是xxxx is not defined咯

2. 闭包：作用域应用的特殊情况
- 函数作为参数被传递
- 函数作为返回值被返回
> 自由变量的查找是在函数定义的地方，向上级作用域查找，而不是在函数执行的地方查找！

3. this，函数执行的时候确认的（在函数调用时根据执行上下文确定）！！！
- 作为普通函数调用，setTimeout话看执行时刻（window）严格模式下this -> undefined
- 使用 call apply bind调用:都是用来改变函数this指向的，call,apply原函数直接调用，bind会返回一个新的函数，这个新函数已经绑定了新的this指向，需手动调用
- 作为对象方法被调用(在执行函数时，如果函数中的 this 是被上一级的对象所调用，那么 this 指向的就是上一级的对象；否则指向全局环境。)
- 在class方法中调用 / 构造函数(如果构造函数中显式返回一个值，且返回的是一个对象，那么 this 就指向这个返回的对象；如果返回的不是一个对象，那么 this 仍然指向实例。)
- 箭头函数：取上级作用域的this

使用call、apply、bind 以及new 对this绑定的是显示绑定；根据调用关系确定的this指向为隐式绑定

```
const obj = {
    name: 'xxx',
    sayHi() {
        console.log(this)
    },
    wait() {
        setTimeout(function() {
            console.log(this)
        })
    },
    waitTwo() {
        setTimeout(()=> {
            console.log(this)
        })
    }
}
```
# 解答
- 手写bind函数

```
Function.prototype.bind1 = function () {
    // 获取参数
    const args = Array.prototype.slice.call(arguments);
    const _this = args.shift();
    const self = this;
    return function () {
        return self.apply(_this, args);
    }
```