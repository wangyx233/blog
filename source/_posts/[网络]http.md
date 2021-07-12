---
title: 【网络】前端开发中的HTTP
categories: ['网络']
tags: ['HTTP']
toc: true
---

# 知识点
- HTTP的缓存机制！！！<strong>重要</strong>
- 状态码
- headers
- Restful API

## 状态码
1. 分类
    - 1xx 服务器收到请求
    - 2xx 请求成功
    - 3xx 重定向
    - 4xx 客户端错误
    - 5xx 服务端错误
2. 常见
    - 200 成功
    - 301 永久重定向（配合location新地址，浏览器自动处理，下次会直接访问新的地址）
    - 302 临时重定向（配合location新地址，浏览器自动处理，下次还会访问旧的地址）短网址，百度之类的搜索引擎的链接，why？
    - 304 资源未修改，缓存 <strong>注</strong>
    - 307 internal redirect
    - 403 没有权限
    - 404 not found
    - 500 服务器错误
    - 502
    - 504 网关超时，服务器内部
3. 协议规范

## Restful API
> API设计方法，把每个URL当做唯一的资源标识。
如何设计一个资源？
- 尽量不用url参数，如：/api/list?page=2  -> /api/list/2
- 用method表示操作类型，POST,PATCH/PUT,DELETE,GET
  
## HTTP Headers
1. 常见的Request Headers
    - Accept 浏览器可以的数据格式
    - Accept-Encoding 浏览器可支持的压缩算法，如：gzip
    - Accept-Language 浏览器支持的语言
    - Connection: keep-alive 保持TCP连接
    - Cookie
    - Host
    - User-Agent 
    - Content-Type 发送数据格式，如：application/json
    - Authorization token验证
2. 常见的Response Headers
    - Content-Type 发送数据格式，如：application/json
    - Content-Length 返回数据的大小，多少字节
    - Content-Encoding 返回数据的压缩算法，如：gzip
    - Set-Cookie 服务端改cookie
3. 自定义headers
4. 用于缓存的headers

## HTTP缓存机制
什么是缓存？为什么需要缓存？哪些资源可以被缓存->js/css/img静态资源和业务数据，html不能被缓存
   > 页面加载更快，网络请求慢，减少网络请求的体积和数量，也是不稳定的

1. 强制缓存
服务端控制什么资源可以缓存，通过Response Headers中的cache-control 控制过期时间
Cache-Control的值
- max-age=86400(秒)
- no-cache 不用强制缓存，去服务端处理
- no-store 不用强制缓存，也不用服务器缓存
- private 允许用户客户端做缓存
- public 允许中间如cdn
Expires也是做缓存过期控制的，已被Cache-Control代替，以Cache-Control为主

2. 协商缓存(对比缓存)
服务端缓存策略，服务端来判断是否用缓存，即服务端判断客户端资源是否和服务端资源一样。如果一致则返回304，否则返回200和最新资源
注意：资源标识
- Response Headers里Last-Modified：资源最后修改时间(精确到秒级)，Etag资源的唯一标识(优先级高，资源被重复生成而内容不变)
- 再次请求的时候，If-Modified-Since: Last-Modified时间(Request Headers里)
- Etag，If-None-Match: Etag的值(Request Headers里)
两者共存会优先使用Etag，Last-Modified只能精确到秒级

整个缓存过程！

## 刷新页面
1. 正常操作：浏览器url，跳转，前进后退等。
> 强制缓存有效，协商缓存有效 
2. 手动刷新，F5，刷新按钮
> 强制缓存失效，协商缓存有效 
3. 强制刷新
> 强制缓存失效，协商缓存失效