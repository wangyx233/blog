---
title: 【JS基础】原型与原型链
categories: ['JavaScript']
toc: true
---

# 思考
1. 如何判断一个变量是不是数组？
2. class的原型本质

<!--more-->
# 知识点
## class
- constructor
- 属性
- 方法
```
class Demo {
    constructor(content) {
        this.content = content;
    }
    hello() {
        console.log(`hello ${this.content}`);
    }
}
const newDemo = new Demo('world');
newDemo.hello();
```
## 继承
- extends
- super
- 扩展/重写方法

```
class SubDemo extends Demo{
    constructor(content, subcontent) {
        super(content);
        this.subcontent = subcontent;
    }
    show() {
        console.log(`hello ${this.content} ${this.subcontent}`);
    }
}
const sDemo = new SubDemo('world', 'hoho');
sDemo.hello();
sDemo.show();
```
## 类型判断 instanceof
```
sDemo instanceof SubDemo // true
sDemo instanceof Demo // true
sDemo instanceof Object // true

[] instanceof Array // true
[] instanceof Object // true
```
## 原型
```
typeof Demo // 'function'
typeof SubDemo // 'function'

// 隐式原型，显示原型
console.log(sDemo.__proto__)
console.log(SubDemo.prototype)
console.log(sDemo.__proto__ === SubDemo.prototype) // true
``` 

### 原型关系

* 每个class都有显示原型prototype
* 每个实例都有隐式原型__proto__
* 实例的__proto指向对应class的prototype

### 基于原型的执行规则

> 实例获取属性和方法时，先从自身的属性和方法中寻找，如果找不到则自动到__proto__中查找

## 原型链

```
console.log(SubDemo.prototype.__proto__)
console.log(Demo.prototype === SubDemo.prototype.__proto__) // true

Demo.prototype.__proto__ === Object.prototype
Object.prototype.__proto__ === null
``` 

> 所以 variable instanceof 构造函数类型，即根据原型链看variable的隐式原型__proto__是不是在其判断的显示原型上

{% img /gallery/prototype_demo.png %}
