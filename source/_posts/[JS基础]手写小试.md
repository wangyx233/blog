---
title: <JS基础>手写小试
reward: true
---

#### 一、数组扁平化

如何将一个多维数组变成一个一维数组，如：

```
const arr = [1, [2, [3, [4, [5]]]]]
// => [1,2,3,4,5]
```

看到数组扁平，第一想到的是 concat()，然后是递归，依次实现一番。

(1) 递归

```
function flat(arr, depth = 1) {
  let res = []
  arr.forEach((elem) => {
    if(!Array.isArray(elem) || !depth){
      res.push(elem)
    }else {
      res = res.concat(flat(elem, depth - 1))
    }
  })
  return res
}
```

(2) reduce()配合 concat()

```
function flat(arr, depth = 1) {
  return depth ? arr.reduce((temp, elem) => (temp.concat(Array.isArray(elem) ? flat(elem, depth - 1) : elem)) , []) : arr
}
```

(3) 内置 API，一顿操作，竟然忽略了 Js Array 的内置 api--flat()，按指定深度递归遍历数组，将所有元素与遍历的子数组中的元素合并为一个新数组返回。

```
var newArray = arr.flat([depth])
const res = arr.flat(Infinity);
```

(4) 正则，另辟蹊径

```
const res1 = JSON.stringify(arr).replace(/\[|\]/g, '').split(','); // 原来数字变成了字符串
const res2 = JSON.parse(`[${JSON.stringify(arr).replace(/\[|\]/g, '')}]`);

```

(5) 迭代

```
function flat(arr, depth = 1) {
  while(arr.some(elem => Array.isArray(elem)) && depth) {
    arr = [].concat(...arr)
    depth--
  }
  return arr
}
```

#### 二、数组去重

```
const arr = [1, 17, '1', 17, true, true, false, false, 'true', 'a', {}, {}];
// => [1, '1', 17, true, false, 'true', 'a', {}, {}]
```

看到数组去重，首先想到的是维护一个 object 用于记录出现过的值然后去掉重复，然后想到 filter 也可以实现类似的

(1) object 方法，普通 object 的 key 都会转换成 String 类型，所以可以借助 ES6 的 Map;

```
const uniq = (arr) => {
  const map = new Map();
  const res = [];
  for (let i = 0; i < arr.length; i++) {
    if (!map.has(arr[i])) {
      map.set(arr[i], true);
      res.push(arr[i]);
    }
  }
  return res;
}
```

(2) filter，过滤得到首次出现的元素。判断元素是否出现过还可以使用 arr.indexOf(elem) === -1 或者 arr.includes(elem)

```
const res = arr.filter((elem, index) => arr.indexOf(elem) === index);
```

(3) ES6 的 Set 数据结构，特性是每个元素都是唯一的，没有重复的值

```
const res = [...new Set(arr)];
[...new Set('ababbc')].join(''); // 'abc'
```

#### 三、深拷贝

```
function cloneDeep(data, exist = new Map()) {
  if(typeof data !== 'object' || data === null) return data
  if(data instanceof Date) return new Date(data)
  if(data instanceof RegExp) return new RegExp(data)
  if(exist.has(data)) return exist.get(data)
  let result = Array.isArray(data) ? [] : {}
  exist.set(data, result)
  const keys = [...Object.keys(data), ...Object.getOwnPropertySymbols(data)]
  keys.forEach(key => {
    result[key] = cloneDeep(data[key], exist)
  })
  return result
}
```

// 两个有序数组[1,3,5]和[2,4,6]，写一个 mergeArr(a,b)合并两个数组，最终得到一个有序数组

\_.get()

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
