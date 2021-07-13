---
title: HTTPS
categories: ['网络']
tags: ['HTTPS']
toc: true
---

HTTP的明文传输带来的问题，可能在传输过程中信息被窃取或者篡改，伪造，容易发生中间人攻击。
HTTP -> 安全层（SSL/TLS） -> 传输层 -> 网络层，其中安全层的作用是对发送的HTTP请求进行加密操作，以及对接收到的HTTP请求进行解密操作。

对称加密，非对称加密的优缺点，得到一个对称加密和非对称加密搭配使用的方式。
传输过程中使用对称加密传输数据，而对称加密所需的密钥则通过非对称加密传输。搭配数字证书，数字证书是向浏览器方明确服务器方的身份，以及提供公钥。
1. 浏览器端向服务端发送请求，包含对称加密套件列表 + 非对称加密套件列表 + client_random
2. 服务端接到请求后，向浏览器端发送选择的，对称加密套件 + 非对称加密套件 + service_random + 数字证书
3. 浏览器端接到响应，先验证数字证书，然后 client_random + service_random 生成 pre_master，用公钥加密pre_master，再加浏览器确认，发送到服务端
4. 服务端接到请求先用私钥解密，得到pre_master，服务器发送确认
5. 开始 client_random + service_random + premaster 生成的 master secret 加密的数据进行传输。
