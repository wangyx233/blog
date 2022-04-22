---
title: 【JS基础】Object对象方法
categories: ["JavaScript"]
toc: true
---

- Object.getPrototypeOf()

> 获得参数对象原型的方法呀。是该对象的`__proto__`指向

```
Object.getPrototypeOf({}) === Object.prototype // true
Object.getPrototypeOf(Object.prototype) === null // true
function f() {}
Object.getPrototypeOf(f) === Function.prototype // true
```

- Object.setPrototypeOf()

> Object.setPrototypeOf(obj, proto)，更改该对象的`__proto__`指向

```
var F = function () {
  this.foo = 'test';
};

var f = new F();
// 等同于
var f = Object.setPrototypeOf({}, F.prototype);
F.call(f);
```

- Object.create()

> 从一个实例生成另一个实例对象

```
if (typeof Object.create !== 'function') {
  Object.create = function (obj) {
    function F() {}
    F.prototype = obj;
    return new F();
  };
}
```

这三种生成新对象的方法是等价的

```
var obj1 = Object.create({});
var obj2 = Object.create(Object.prototype);
var obj3 = new Object();
```

```
var obj = Object.create(null);
```

obj 不具有 Object.prototype 上的属性和方法了。
Object.create 必须提供对象原型，参数为空或者不是对象都会报错。

```
var obj = Object.create({}, {
  p1: {
    value: 123,
    enumerable: true,
    configurable: true,
    writable: true,
  },
  p2: {
    value: 'abc',
    enumerable: true,
    configurable: true,
    writable: true,
  }
});

// 等同于
var obj = Object.create({});
obj.p1 = 123;
obj.p2 = 'abc';
```

```
function A() {}
var a = new A();
var b = Object.create(a);

b.constructor === A // true
b instanceof A // true
```

- Object.prototype.isPrototypeOf()

```
var o1 = {};
var o2 = Object.create(o1);
var o3 = Object.create(o2);

o2.isPrototypeOf(o3) // true
o1.isPrototypeOf(o3) // true

```

```
Object.prototype.isPrototypeOf({}) // true
Object.prototype.isPrototypeOf([]) // true
Object.prototype.isPrototypeOf(/xyz/) // true
Object.prototype.isPrototypeOf(Object.create(null)) // false
```

```
var obj = new Object();

obj.__proto__ === Object.prototype
// true
obj.__proto__ === obj.constructor.prototype
// true
```

> 获取实例对象`obj`的原型对象有哪些方法？

1. obj.**proto**
2. obj.constructor.prototype
3. Object.getPrototypeOf(obj)

```
var P = function () {};
var p = new P();

var C = function () {};
C.prototype = p;
var c = new C();

c.constructor.prototype === p // false
```

所以，在改变原型对象时，一般要同时设置`constructor`属性。

```
C.prototype = p;
C.prototype.constructor = C;

var c = new C();
c.constructor.prototype === p // true
```

- Object.getOwnPropertyNames()

> 对象本身包含的属性名，不包含继承的。可枚举、不可枚举的都包括
> 如果只想获取可枚举的 `Object.keys`

- `in`

  > `in` 运算符返回一个布尔值，表示一个对象是否具有某个属性。它不区分该属性是对象自身的属性，还是继承的属性。常用于检查一个属性是否存在。

- 对象的拷贝
  具有相同的原型，以及实例属性。

```
function copyObject(orig) {
  return Object.create(
    Object.getPrototypeOf(orig),
    Object.getOwnPropertyDescriptors(orig)
  );
}
```

```
function copyObject(orig) {
  var copy = Object.create(Object.getPrototypeOf(orig));
  copyOwnPropertiesFrom(copy, orig);
  return copy;
}

function copyOwnPropertiesFrom(target, source) {
  Object
    .getOwnPropertyNames(source)
    .forEach(function (propKey) {
      var desc = Object.getOwnPropertyDescriptor(source, propKey);
      Object.defineProperty(target, propKey, desc);
    });
  return target;
}
```

### prototype 对象

> 拥有 constructor，指向该原型对象的构造函数
> constructor 属性表示原型对象与构造函数之间的关联关系，如果修改了原型对象，一般会同时修改 constructor 属性，防止引用的时候出错。

- instanceof

```
[] instanceof Array
// 等同于
Array.prototype.isPrototypeOf([])
```

instanceof 的原理是检查右边构造函数的 prototype 属性，是否在左边对象的原型链上。有一种特殊情况，就是左边对象的原型链上，只有 null 对象。这时，instanceof 判断会失真。

> PS: 判断是否是数组的方式

- Array.isArray()
- Object.prototype.toString.call()
- [].constructor.name // 'Array'
- xxx instanceof Array
