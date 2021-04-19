---
title: 【JS基础】异步
categories: ['JavaScript']
toc: true
---

# 思考
- 同步和异步的区别
- 手写Promise
- 前端异步的场景
- event loop（事件循环）的机制，画图
- async/await
- Promise的三种状态，then，catch的连接
  
```
console.log(1)
setTimeout(() => {
    console.log(2)
}, 1000);
console.log(3)
setTimeout(() => {
    console.log(4)
}, 0);
console.log(5)
```
<!--more-->
# 知识点

why?
遇到等待（请求，定时任务）不能卡住
单线程->异步
JS是单线程，异步不会阻塞代码的执行，同步会阻塞代码的执行

why Promise?
- 编码习惯，回调
- callback hell -> 管道串联

1. event loop 事件循环

- 同步代码在call stack一步一步执行
- 遇到异步代码先记录下来
- 等到“时机成熟”，把异步函数放到call queue中
- 如果call stack 为空（即同步代码执行完）Event Loop开始工作
- 轮询查找call queue，如果有则移到call stack 执行
- 然后继续查找

由于JS是单线程的，异步(setTimeout, ajax)，DOM事件也都是基于event loop

2. Promise
  * 三种状态，变化是不可逆的，只能从pending变化。且状态的改变和是否执行 resolve，reject有关
    - pending
    - resolved
    - rejected
  * 状态的变化和表现
    - pending状态，不会触发then和catch
    - resolved状态，会触发后续的then回调
    - rejected状态，会触发后续的catch回调
    > resolved状态和rejected状态可以直接触发，如Promise.resolve()，Promise.reject()
  * then，catch链式调用对状态的影响 
    - then 返回一个Promise，如果有报错状态为rejected，正常则状态为resolved
    - catch 正常状态返回resolved，里面有报错则返回rejected

```
Promise.resolve().then(() => {
    console.log(1) // 1
}).catch(() => {
    console.log(2)
}).then(() => {
    console.log(3) //3
})

Promise.resolve().then(() => {
    console.log(1) // 1
    throw new Error('err1')
}).catch(() => {
    console.log(2) // 2
}).then(() => {
    console.log(3) //3
})

Promise.resolve().then(() => {
    console.log(1) // 1
    throw new Error('err1')
}).catch(() => {
    console.log(2) // 2
}).catch(() => {
    console.log(3)
})

```

3. async/await
- callback hell
- Promise 回调函数
- async/await同步的语法