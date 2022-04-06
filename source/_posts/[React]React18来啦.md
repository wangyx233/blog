---
title: 【React】React 18来辣
categories: ["React"]
toc: true
---

铛铛铛，React18正式发布辣

> 前置知识： Concurrent(并发)

### Concurrent
> 渲染是可中断的。（以前React中update是同步渲染，一旦update开启，在任务完成前都是不可中断的。

在Concurrent模式下，update开始了可以中断，结果是可以继续，也可以选择遗弃。
- 可中断。任务是有优先级，优先级高的比如用户交互，输入，点击...fiber的链表结构，指向改变。
- 可被遗弃。比如，一个操作过程，中间的可能切走，不重要或者过期了，所以可以被遗弃，只关注最后的展示。
- 状态复用。OffScreen，比如页面回退了，又重新进入了；或者即将要渲染的UI进行缓存准备。

> 总结: 并不是API之类的新特性，而是新特性的基础，包括Suspense、transitions等。 

### 新特性

#### createRoot
```
const root = createRoot(document.getElementById('root'))
root.render(<App />)
root.render(<App2 />)
```

#### 自动批量处理 Automatic Batching

> setState是同步还是异步呀，可以实现同步嘛，怎么实现，异步的原理是什么？

18之前：都可以，同步：setTimeout，或者原生事件里同步。异步：批量处理，合成事件里，把多次的render合并起来，关键是依赖合成事件。
18之后，同步可用flushSync。就不要用setTimeout，也是异步了，why？不是依靠合成事件，而是自动批量处理。
```
flushSync(() => {
  // state 变化  
})
```

#### Suspense
加载等待，类似loading

```
<Suspense fallback={<Spin />}>
  <Component />
</Suspense>
```

..._(¦3」∠)_下回再战...