---
title: 【JS基础】来，让我们手把手实现Promise 🐶
categories: ["JavaScript"]
toc: true
---

学了又好像没学之二 🤦🏻‍♀️

平时开发中，习惯使用`new Promise((resolve, reject) => {})` 来进行异步请求的调用。那 Promise 到底是个啥呢，能不能自己实现一下呢？

> 不能就没有聊下去的意义了(并不是) 🤐

Promise/A+ 的规范，https://promisesaplus.com/

简单梳理一下，

1. Promise 其实是一个构造函数，我们用这个构造函数创建一个 Promise 实例。这个构造函数只有一个参数，`executor`。这个参数是一个函数，具有`resolve`，`reject`两个方法作为参数。

2. Promise 返回的这个 promise 对象实例，具有`then`方法。在`then`方法中，可以定义两个参数`onfulfilled`和`onrejected`，都是函数类型。其中`onfulfilled`的参数，可以获取 promise 对象`resolved`的值；`onrejected`则是`rejected`的值。

3. 在使用 new 关键字调用 Promise 构造函数时，往往是异步结束时，调用 executor 的参数 resolve 方法，并将 resolved 的值作为 resolve 函数参数执行，这个值便可以后续在 then 方法第一个函数参数（onfulfilled）中拿到；在出现错误时，调用 executor 的参数 reject 方法，并将错误信息作为 reject 函数参数执行，这个错误信息可以在后续的 then 方法第二个函数参数（onrejected）中拿到。

> 所以，由于 Promise 状态的唯一性，需要一个变量来存状态（pending，fulfilled，rejected），以及一个变量来存改变后的值。同时还要提供 resolve 方法以及 reject 方法，作为 executor 的参数

> 为什么 then 放在 Promise 构造函数的原型上，而不是放在构造函数内部呢？

每个 promise 实例的 then 方法逻辑是一致的，在实例调用该方法时，可以通过原型（Promise.prototype）找到，而不需要每次实例化都新创建一个 then 方法，这样节省内存，显然更合适。

4.  promise 实例状态只能从 pending 改变为 fulfilled，或者从 pending 改变为 rejected。状态一旦变更完毕，就不可再次变化或者逆转。也就是说：如果一旦变到 fulfilled，就不能再 rejected，一旦变到 rejected，就不能 fulfilled。

5.  异步的实现

- 在“合适”的时间去调用 onfulfilled 方法呀，即`resolve`调用的时刻。所以状态是`pending`的状态，先存一下`onfulfilled`方法，在`resolve`方法中再执行。
- 结合事件循环理解，这里需要将 resolve 和 reject 的执行，放到任务队列中。
- 为了保证 Promise 属于 microtasks，很多 Promise 的实现库用了 MutationObserver 来模仿 nextTick）
- 还需要判断`resolve`的值是不是 promise，如果是的话 `return value.then(resolve, reject)`
- 多个`then`方法，那`onfulfilled`，`onrejected`维持数组，依次执行。
- 错误处理的话，`executor` 可以用`try...catch`捕获一下

6. 链式调用

一个 Promise 实例的 then 方法中的 onfulfilled 函数和 onrejected 函数中，是支持再次返回一个 Promise 实例的，也支持返回一个非 Promise 实例的普通值；并且返回的这个 Promise 实例或者这个非 Promise 实例的普通值将会传给下一个 then 方法 onfulfilled 函数或者 onrejected 函数中，这样就支持链式调用了。

> 所以，为了能够支持 then 方法的链式调用，那么每一个 then 方法的 onfulfilled 函数和 onrejected 函数都应该返回一个 Promise 实例。

7. 给 .then() 函数传递非函数值作为其参数时，实际上会被解析成 .then(null)，这时候的表现应该是：上一个 promise 对象的结果进行“穿透”，如果在后面链式调用仍存在第二个 .then() 函数时，将会获取被穿透下来的结果。

<!--more-->

```
Promise.resolve(1)
  .then(2)
  .then(Promise.resolve(3))
  .then(console.log)

// 1
```

链式调用：是指`then`也会返回一个`promise`，如果返回值不是 promise，则会包装成 Promise.resovle()，在此基础上继续调用。如果它们之间没有联系，则会独立调用，比如：p.then(); p.then()....。
`then`按顺序执行完会进行下一个`then`，`return`的结果是下一个的 value
即下一个异步任务依赖上一个异步任务的结果。

> 事实上 then 方法不会触发回调，也不会将它放到微任务，then 只负责注册回调，由 resolve 将注册的回调放入微任务队列，由事件循环将其取出并执行。

如果 then 中的回调返回了一个 promise，那么 then 返回的 promise 会等待这个 promise 被 resolve 后再 resolve

```
Promise.resolve().then(() => {
  return new Error('error!!!')
}).then(res => {
  console.log("then: ", res)
}).catch(err => {
  console.log("catch: ", err)
})
```

```
new Promise((resolve, reject) => {
  console.log(0)
  resolve()
  console.log(1)
}).then(res => {
  new Promise((resolve, reject) => {
      console.log(2)
      resolve()
    })
    .then(res => {
      console.log(3)
    }).then(res => {
      console.log(4)
    }).then(res => {
      console.log(7)
    })
}).then(res => {
  console.log(5)
}).then(res => {
  console.log(6)
}).then(res => {
  console.log(8)
})

// 0 1 2 3 5 4 6 7 8

new Promise((resolve, reject) => {
  console.log(0)
  resolve()
  console.log(1)
}).then(res => {
  return new Promise((resolve, reject) => {
      console.log(2)
      resolve()
    })
    .then(res => {
      console.log(3)
    }).then(res => {
      console.log(4)
    })
}).then(res => {
  console.log(5)
})

// 0 1 2 3 4 5

setTimeout(() => {
  console.log(0)
}, 0)
new Promise((resolve, reject) => {
  for (var i = 0; i < 10000; i++) {
    if (i === 9999) {
      resolve()
    }
  }
  console.log(i)
  console.log(1)
}).then(res => {
  console.log(2)
})
console.log(3)

// 10000 1 3 2 0
```

- 不管是 then 方法还是 catch，都会执行 finally，finally 不接受参数。finally 方法总是会返回原来的值。
  finally 方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是 fulfilled 还是 rejected。这表明，finally 方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。执行了 finally 后，PromiseResult 是 undefined

```
Promise.resolve('2')
  .finally(() => {
    console.log('finally2')
    return '我是finally2返回的值'
  })
  .then(res => {
    console.log('finally2后面的then函数', res)
  })

// finally2
// finally2后面的then函数 2
```

```
function runAsync(x) {
  const p = new Promise(r => setTimeout(() => r(x, console.log(x)), 1000))
  return p
}

function runReject(x) {
  const p = new Promise((res, rej) => setTimeout(() => rej(`Error: ${x}`, console.log(x)), 1000 * x))
  return p
}
Promise.all([runAsync(1), runReject(4), runAsync(3), runReject(2)])
  .then(res => console.log(res))
  .catch(err => console.log(err))
```

如果 async 函数中抛出了错误，就会终止错误结果，不会继续向下执行。如果想要让错误不足之处后面的代码执行， 可以使用 catch 来捕获

```
async function async1 () {
  await async2();
  console.log('async1');
  return 'async1 success'
}
async function async2 () {
  return new Promise((resolve, reject) => {
    console.log('async2')
    reject('error')
  })
}
async1().then(res => console.log(res))

```

- 简单的实现

```
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';
class Promise {
  constructor(executor) {
    this.status = PENDING
    this.value = null
    this.reason = null
    this.onFulfilledArray = []
    this.onRejectedArray = []

    const resovle = value => {
      if (value instanceof Promise) {
        return value.then(resovle, reject)
      }
      setTimeout(() => {
        if (this.status === PENDING) {
          this.value = value
          this.status = FULFILLED
          this.onFulfilledArray.forEach(func => {
            func(value)
          })
        }
      })
    }

    const reject = reason => {
      setTimeout(() => {
        if (this.status === PENDING) {
          this.reason = reason
          this.status = REJECTED
          this.onRejectedArray.forEach(func => {
            func(reason)
          })
        }
      })
    }

    try {
      executor(resovle, reject)
    } catch (error) {
      reject(error)
    }

  }

  then(onfulfilled, onrejected) {
    onfulfilled = typeof onfulfilled === 'function' ? onfulfilled : data => data
    onrejected = typeof onrejected === 'function' ? onrejected : error => {
      throw error
    }

    let promise2 = new Promise((resovle, reject) => {
      if (this.status === FULFILLED) {
        setTimeout(() => {
          try {
            let result = onfulfilled(this.value)
            resolvePromise(promise2, result, resovle, reject)
          } catch (error) {
            reject(error)
          }
        })
      }

      if (this.status === REJECTED) {
        setTimeout(() => {
          try {
            let result = onrejected(this.value)
            resolvePromise(promise2, result, resovle, reject)
          } catch (error) {
            reject(error)
          }
        })
      }

      if (this.status === PENDING) {
        this.onFulfilledArray.push(() => {
          try {
            let result = onfulfilled(this.value)
            resolvePromise(promise2, result, resovle, reject)
          } catch (error) {
            reject(error)
          }
        })
        this.onRejectedArray.push(() => {
          try {
            let result = onrejected(this.value)
            resolvePromise(promise2, result, resovle, reject)
          } catch (error) {
            reject(error)
          }
        })
      }
    })
    return promise2
  }

  catch (catchFunc) {
    return this.then(null, catchFunc)
  }

  static resovle(value) {
    return new Promise((resovle, reject) => {
      resovle(value)
    })
  }
  static reject(value) {
    return new Promise((resovle, reject) => {
      reject(value)
    })
  }
  static race(promiseArray) {
    if (!Array.isArray(promiseArray)) {
      throw new TypeError('The arguments should be an array!')
    }
    return new Promise((resolve, reject) => {
      for (let i = 0; i < length; i++) {
        promiseArray[i].then(data => resolve(data)).catch(error => reject(error))
      }
    })
  }
  static all(promiseArray) {
    if (!Array.isArray(promiseArray)) {
      throw new TypeError('The arguments should be an array!')
    }
    return new Promise((resolve, reject) => {
      let result = []
      const length = promiseArray.length
      for (let i = 0; i < length; i++) {
        promiseArray[i].then(data => {
          result[i] = data
          if (result.length === length) {
            resolve(result)
          }
        }).catch(error => reject(error))
      }
    })
  }
}


function resolvePromise(promise2, result, resovle, reject) {
  if (promise2 === result) {
    reject(new TypeError('error due to circular reference'))
  }

  if (typeof result === 'object' && result !== null || typeof result === 'function') {
    let called = false
    try {
      then = result.then
      if (typeof then === 'function') {
        then.call(result, value => {
          if (called) {
            return
          }
          called = true
          return resolvePromise(promise2, value, resovle, reject)
        }, error => {
          if (called) {
            return
          }
          called = true
          return reject(error)
        })
      } else {
        resovle(result)
      }
    } catch (error) {
      if (called) {
        return
      }
      called = true
      reject(error)
    }
  } else {
    resovle(result)
  }
}
```
