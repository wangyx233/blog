---
title: 【WebAPI】BOM
categories: ['JavaScript']
toc: true
---

> BOM -> Browser Object Model

- 如何识别浏览器的类型
- 分别拆解url各部分

# 知识点
- navigator
- screen
- location
- history
```
// 1. navigator
const ua = navigator.userAgent;

// 2. screen
const width = screen.width;
const height = screen.height;

// 3. location
location.href
location.protocol
location.pathname
location.hash
location.search
location.host

// 4. history
history.back();
history.forwards();
```