---
title: 【WebAPI】存储
categories: ['JavaScript']
toc: true
---

- 介绍cookie、localStorage、sessionStorage区别

# 知识点

1. cookie
 - 通过;分割的字符串，每一部分都是key=value的形式
 - http请求的一部分，用于浏览器和服务端通讯的
 - document.cookie修改，默认每次追加或者修改一个key/value
 - 限制：最大4KB，每次http请求都需要发送到服务端，api太简陋

2. localStorage、sessionStorage
 - HTML5专门为存储设计的，最大可存储5M / pre host
 - API：getItem() setItem() 存储的为字符串的形式，所以其他格式的数据要注意
 - 和http请求没关系
 - localStorage 永久存储，除非手动或者代码删除；sessionStorage只存在于当前会话，页面关闭清空

