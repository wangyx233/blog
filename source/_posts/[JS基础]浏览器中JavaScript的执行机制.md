---
title: 【JS基础】浏览器中JavaScript执行机制
categories: ['JavaScript']
toc: true
---

## 变量提升（Hoisting）
### What?
> JavaScript代码执行过程中，JS引擎会把变量声明和函数声明部分提升到代码的开头，并不是物理性的移动，实际中代码并没有改变，而是JS引擎在编译阶段放进内存的。
一段JS代码在执行时，先经过编译，编译完成后，再进入执行阶段。
编译后的内容分为两部分：执行上下文（Execution context）和可执行代码。
```
showName()
console.log(myname)
var myname = 'xxx'
function showName() {
  console.log('函数showName被执行');
}
// 编译
var myname;
function showName() { //函数定义放到堆中，变量环境中showName指向该函数
  console.log('函数showName被执行');
}

// 执行
showName()
console.log(myname)
myname = 'xxx'
```

### QA 代码中相同的变量和函数如何处理？
```
function showName() {
    console.log('aaa');
}
showName();
function showName() {
    console.log('bbb');
}
showName();

```
<!--more-->
前者会被覆盖

## 调用栈
执行上下文：
- 全局上下文
- 函数上下文

### What?
> 利用栈的结构来管理上下文，调用栈是JS引擎追踪函数执行的一个机制
- Chrome 浏览器Sources -> Call Stack 可查看
- console.trace()

### 栈溢出（Stack Overflow）
> 调用栈是有大小的，当入栈的执行上下文超过一定数目，JS引擎就会报错，即为栈溢出 {超过了最大栈调用大小（Maximum call stack size exceeded）}

## 作用域
> 变量和函数可以访问的范围，即作用域控制着变量和函数的可见性和生命周期

ES6
- 全局作用域
- 函数作用域，作用于函数内部
- 块级作用域，作用于{}内部，let，const

```
function letTest() {
  let x = 1;
  if (true) {
    let x = 2;  // 不同的变量
    console.log(x);  // 2
  }
  console.log(x);  // 1
}
```
作用块内声明的变量不影响块外面的变量

### How? 如何支持块级作用域的

代码编译生成执行上下文时，
- var 声明的变量会放到变量环境
- let / const 声明的变量被放到词法环境（Lexical Environment）

## 作用域链
在每个执行上下文中都包含了一个外部引用，用来指向外部的执行上下文，这个外部引用成为outer。
> 当一个变量使用，会在当前执行上下文中寻找，如果没有则会在outer指向的执行上下文中寻找，这个查找链条即为作用域链。在JS执行过程中，作用域链是由词法作用域决定的。

### 词法作用域
> 词法作用域是指作用域由代码中函数声明的位置来决定的，词法作用域是静态的作用域，由此可以预测代码运行过程中的行为。

注：词法作用域是在代码阶段就决定好的，和函数怎样调用没有关系

## 闭包
> 根据词法作用域的规则，内部函数总能访问外部函数中声明的变量，当通过调用一个外部返回一个内部函数后，即使外部函数已经执行结束了，但内部函数引用外部函数的变量仍保存在内存中，这些变量的集合称之为闭包。

Chrome / Sources / Scope 顺序 Local -> Closure -> Global

## this
注： 作用域链和this不同

> this是和执行上下文绑定的，每个执行上下文都有一个this
- 全局执行上下文中的this -> Window
- 函数执行上下文中的this，谁调用this就指向谁，箭头函数不会创建自己的执行上下文，所以this为外部函数的this
```
function foo(){
  console.log(this)
}
foo()
```

### 改变this指向

- call，foo.call(obj)
- bind
- apply
- 对象调用方法设置，使用对象来调用内部的方法，该方法的this指向对象
- 构造函数中设置

```
function CreateObj(){
  this.name = " test "
}
var myObj = new CreateObj()
```
通过new来创建对象的过程，this指向新对象本身

```
var tempObj = {}
CreateObj.call(tempObj)
return tempObj
```

### this存在的问题

```
var myObj = {
  name : " test ",
  showThis: function(){
    console.log(this)
    function bar(){console.log(this)} // Window
    bar()
  }
}
myObj.showThis() // myObj

```

1. 嵌套函数的this不会从外部函数中继承，根据自身的执行上下文，有自己的this

如果想要继承怎么办？
- 如果想在内部函数中用外部函数的this，可以用一个变量中转一番，把外部的this存为一个变量，在内部函数中使用
- 使用箭头函数

2. 普通函数中的this指向Window
在严格模式下，默认执行一个函数，其函数的执行上下文中的 this 值是 undefined

