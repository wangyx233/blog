---
title: 【JS基础】变量类型与比较
categories: ['JavaScript']
toc: true
---
# 思考
1. typeof 能判断什么类型？
2. === vs == 有什么不同？
3. 值类型与引用类型有什么区别？
4. 手写深拷贝？
   
<!--more-->

# 知识点
## 值类型 | 引用类型
> 值类型包括，string，number，boolean，undefined，symbol 值类型是存储在栈中的，存的是值本身

> 常见的引用类型包括，object，array，null(特殊的引用类型，只向空地址)，function(特殊的引用类型，也可以独立为函数类型)  引用类型则是在栈中存变量以及变量指向的内存地址，实际的值是由内存地址指向存在堆中的，普通的浅拷贝，复制的是栈中的内容（考虑到存储，性能）

## typeof

- 能区分是值类型还是引用类型，并且能够区分具体的值类型，但是无法细化区分引用类型
- 能识别出function类型

## 深拷贝
递归实现，function内容
```
https://codepen.io/yx233/pen/oNzKvbg
```

## 类型转换
- 字符串拼接，和字符串的加法
- ==，类型转换，null == undefined // true, 除了 == null(=== null || === undefined)，其他情况用===判断
- if，逻辑判断，判断的是truely变量 !!a === true，falsely变量 !!b === false。 && || 返回终止前的值
```
👇为falsely变量，除此以外为truely变量
!!0 === false
!!null === false
!!undefined === false
!!NaN === false
!!'' === false
!!false === false
```