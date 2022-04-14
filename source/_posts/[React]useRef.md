---
title: 【React】Hook系列 - useRef
categories: ["React"]
tags: ["React"]
toc: true
---

平时用函数组件进行 React 开发，hooks 想必经常用，像最长用的

- useState
- useEffect，传[] 只渲染一次，不传每次都渲染。异步的呀
- useMemo
- useCallback

这里先主要说一下`useRef`，主要的关注点是：

```
const ref = useRef(initValue)
```

- 返回一个可变的 ref 对象，该对象只有个 current 属性，初始值为传入的参数( initialValue )。
- 返回的 ref 对象在组件的整个生命周期内保持不变，即引用不变。
- 并且当更新 current 值时并不会进行 re-render
- 一般会在`useEffect`，或者事件处理内使用

> 总结来看，`useRef`定义的对象，不会导致组件重新渲染。并且它的`current`值可以突破闭包的限制，不会因为组件更新而重置。

- useRef vs createRef
  > createRef 每次会返回新的引用，即会随着组件的更新而重新创建

#### 日常使用

1. 自定义一个保持之前值的 hook

```
import {useRef, useEffect} from 'react'
export function usePrevious<T>(value: T): T | undefined {
  const ref = useRef<T | undefined>()
  useEffect(() => {
    ref.current = value
  }, [value])
  return ref.current
}
```

2. 判断是不是第一次更新

```
import {useRef} from 'react'
export function useIsFirstRender(): boolean {
  const ref = useRef<boolean>(true)
  if(ref.current) {
    ref.current = false
    return true
  }
  return false
}
```
