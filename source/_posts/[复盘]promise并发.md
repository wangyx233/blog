---
title: 【复盘】Promise并发咋整
categories: ["JavaScript"]
toc: true
---

```
class Scheduler {
  constructor() {
    this.queue = []
    this.max = 2
    this.count = 0
  }

  add(promiseCreator) {
    return new Promise((resolve, reject) => {
      this.queue.push({
        fn: promiseCreator,
        resolve,
        reject
      })
      this.run()
    })
  }

  run() {
    if (this.count < this.max && this.queue.length) {
      this.count++
      const {
        fn,
        resolve,
        reject
      } = this.queue.shift()
      fn().then(value => {
        resolve()
        this.count--
        this.run()
      }).catch(reason => reject())
    }
  }
}

const timeout = time => new Promise(resolve => setTimeout(() => resolve(), time))

const scheduler = new Scheduler()
const addTask = (time, order) => {
  const result = scheduler.add(() => timeout(time))
  result.then(() => console.log(order + 'order'))
}

addTask(1000, '1')
addTask(500, '2')
addTask(300, '3')
addTask(400, '4')
// 2 3 1 4
```

当时只想到了如何控制并发，没有捋清楚整个流程，尤其是`resovle`的处理。

（还得好好看看 Promise 的实现...
