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
- 函数作用于
- 块级作用域（ES6）
> 自由变量，一个变量在当前作用域没有定义却被使用了，需要沿着作用域一层一层向上查找，找到为止，如果全局作用域都没有找到，那就是xxxx is not defined咯

2. 闭包：作用域应用的特殊情况
- 函数作为参数被传递
- 函数作为返回值被返回
> 自由变量的查找是在函数定义的地方，向上级作用域查找，而不是在函数执行的地方查找！

3. this，函数执行的时候确认的
- 普通函数，setTimeout话看执行时刻
- call apply bind: call,apply原函数直接调用，bind会返回一个新的函数
- 对象方法
- class
- 箭头函数：取上级作用域的this

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