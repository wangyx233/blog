---
title: 【JS基础】异步
categories: ["JavaScript"]
toc: true
---

# 思考

- 同步和异步的区别
- 手写 Promise
- 前端异步的场景
- event loop（事件循环）的机制，画图
- async/await
- Promise 的三种状态，then，catch 的连接

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
JS 是单线程，异步不会阻塞代码的执行，同步会阻塞代码的执行

why Promise?

- 编码习惯，回调
- callback hell -> 管道串联

1. event loop 事件循环

- 同步代码在 call stack 一步一步执行
- 遇到异步代码先记录下来
- 等到“时机成熟”，把异步函数放到 call queue 中
- 如果 call stack 为空（即同步代码执行完）Event Loop 开始工作
- 轮询查找 call queue，如果有则移到 call stack 执行
- 然后继续查找

由于 JS 是单线程的，异步(setTimeout, ajax)，DOM 事件也都是基于 event loop

2. Promise

- 三种状态，变化是不可逆的，只能从 pending 变化。且状态的改变和是否执行 resolve，reject 有关
  - pending
  - resolved
  - rejected
- 状态的变化和表现
  - pending 状态，不会触发 then 和 catch
  - resolved 状态，会触发后续的 then 回调
  - rejected 状态，会触发后续的 catch 回调
    > resolved 状态和 rejected 状态可以直接触发，如 Promise.resolve()，Promise.reject()
- then，catch 链式调用对状态的影响
  - then 返回一个 Promise，如果有报错状态为 rejected，正常则状态为 resolved
  - catch 正常状态返回 resolved，里面有报错则返回 rejected

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
- async/await 同步的语法
