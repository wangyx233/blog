---
title: 浏览器の网络-HTTP
categories: ['网络']
tags: ['HTTP']
toc: true
---

## HTTP
HTTP是应用层协议

### HTTP/1
- HTTP/0.9 -> 1991出现的，依靠TCP(传输层协议)，三次握手建立连接，四次挥手断开连接。只有一个请求行类似GET /index.html，没有请求头和请求体，服务端也没有返回头信息，用ASCII字节码来传递HTML消息

- HTTP/1.0，增加了请求头和响应头，并且以Key-Value的形式保存，客户端在发起请求的时候会带上请求头和请求行，然后服务端在返回信息的时候也会返回响应头和响应体。
那是怎样支持多种文件传输的呢？请求头应该包含什么信息，告诉服务端需要怎样的数据？
文件的类型,文件编码是什么，是否压缩，什么类型语言...
```
Accept: text/html
Accept-Encoding: gzip, deflate, br
Accept-Charset: ISO-8859-1,utf-8
Accept-Language: zh-CN,zh

```
<!--more-->
  服务器接收到了请求头，会根据请求头的要求准备数据，如果服务器没有符合请求头的要求，那会返回服务端拥有的

  ```
  Content-Encoding: gzip
  Content-Type: application/json // text/html; charset=UTF-8
  ```
  然后浏览器会根据浏览器的返回来处理文件

  除了可以处理多种文件外，HTTP/1.0，还引入了状态码，Cache，用户代理等特性

- HTTP/1.1
1. 增加了持久链接，不需要每次传输数据，都进行TCP链接。即在一次TCP链接里可以传输多个HTTP请求，只要浏览器或者服务端没有明确的断开连接，这个TCP连接一直保持。优势是可以减少TCP连接和断开的次数，减轻服务端的压力，提升HTTP的请求时长。
持久连接在HTTP/1.1中是默认打开的，不用特地设置打开，关闭的话在请求头上加上Connection: close即可。Connection: keep-alive
目前浏览器，同一个域名默认允许同时建立6个TCP持久连接
2. HTTP1.1 管线，虽然TCP可以保持连接，但是HTTP请求需要等到有返回再发下一个请求，即是顺序的。如果TCP通道中的某个请求因为各种原因没有返回，那会阻塞后面其他的请求 -> 队头堵塞。所以HTTP1.1，支持将多个HTTP请求批量发送到服务器，但是服务端还是根据请求的顺序来返回。所以...FireFox、Chrome都放弃了管线化
3. 提供了虚拟主机的支持，请求头中增加了Host，一个服务器可以支持多个域名，服务端也可以根据Host，来进行不同的响应
4. Transfer-Encoding: chunked。可以对动态生成内容支持。HTTP/1.0 中需要在响应头中设置完整的数据大小，如Content-Length: 101。HTTP/1.1支持Chunk transfer ，可以把数据分成任意大小数据块，每个数据块附上上一个数据块的大小，最后发送一个零长度的数据块作为结束。
5. 支持客户端Cookies、安全机制。

### HTTP/2
RTT round-trip time 来回通讯往返时间
HTTP1.1 存在的问题，HTTP/1.1的带宽利用率不理想，带宽：

### HTTP/3

