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
- 微任务/宏任务区别
- Promise的三种状态，then，catch的连接
- Promise setTimeout

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