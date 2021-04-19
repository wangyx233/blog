---
title: 【WebAPI】AJAX
categories: ['JavaScript']
toc: true
---

- 手写一个简易的ajax
- 跨域

# 知识点
- XMLHttpRequest
- 状态码
- 跨域： 同源策略，跨域解决方案

## 1. XMLHttpRequest
```
const xhr = new XMLHttpRequest();
xhr.open('method', 'url', 'true/false'); // 是否是异步
xhr.onreadystatechange = function () {
    if(xhr.readyState === 4) {
        if(xhr.status === 200) {
            console.log(JSON.parse(xhr.responseText))
        }
    }
}
xhr.send(null)
// post
xhr.send(JSON.stringify({}))
```
- xhr.readyState
 * 0 (未初始化)没有调用send()方法
 * 1 (载入)已调用send()，正在发送请求
 * 2 (载入完成)send()方法执行完成，已经接收到全部响应内容
 * 3 (交互)正在解析响应内容
 * 4 (完成)响应内容解析完成，可在客户端调用

- xhr.status http状态码
  * 2xx 成功处理请求
  * 3xx 重定向，浏览器直接跳转 301：永久重定向 302：临时重定向 304：资源未改变，用缓存
  * 4xx 客户端请求错误，404：not found 403：客户端没有权限
  * 5xx 服务端错误

## 2. 跨域
 - 什么是跨域 同源策略
 - JSONP
 - CORS

 ### 同源策略
 > 同源：协议，域名，端口三者一致。 Ajax请求时， <strong>浏览器</strong>要求当前页面和server必须同源。CSP安全
 加载图片css，js可无视同源策略
 - img src='跨域的图片地址'，可用于统计埋点
 - link href='跨域的css地址' ，可以使用CDN
 - script src='跨域的js地址'，可以使用CDN，实现JSONP
所有的跨域，都必须经过server端的允许和配合

 ### JSONP 


