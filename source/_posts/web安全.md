---
title: 同源策略与Web安全
categories: ['安全']
tags: ['安全']
toc: true
---
# 安全问题
Web页面安全，浏览器网络安全，浏览器安全

# 同源策略 Same-origin policy
什么是同源？
> 两个URL的协议、域名、端口都相同，则这两个URL同源
```
http://www.yixuanwang.cn/?article=1
http://www.yixuanwang.cn/?article=0
```
相同源之间可以互相访问资源和操作DOM结点的，不同源之间会有一定安全策略的制约，即为同源策略。
不同源之间不能对DOM对象进行读写操作，不能读取Cookie、LocalStorage等数据，也不能通过XMLHttpRequest传递数据。
如果想做以上操作怎么办？
- 页面引用第三方资源可能会导致XSS攻击 -> CSP 内容安全策略，让服务器决定浏览器能够加载哪些资源，让服务器决定浏览器是否能够执行内联 JavaScript 代码
- 安全地进行数据传递，CORS跨域资源共享
- 安全地相互操作DOM，跨文档消息机制window.postMessage

## XSS攻击-Cross Site Scripting 跨站脚本
### 是什么
XSS是指攻击者向HTML，DOM中注入恶意脚本，从而在用户浏览页面时通过恶意注入的脚本对用户实施攻击。
可能会获取用户的Cookies，监控用户的行为“addEventListener”，也可能通过DOM伪造假的登录窗口，也可能生成浮窗广告，影响体验。
- 存储型XSS，恶意脚本插入网站的数据库里，再读取的时候会进行攻击。
- 反射型 XSS 攻击，在请求连接里注入恶意脚本，将请求提交给Web服务器，Web服务器收到请求，又将恶意代码反射给了浏览器
- 基于 DOM 的 XSS 攻击，不涉及服务器，通过页面劫持等方法将恶意脚本注入页面，修改web页面
### 怎么办

- 服务器对输入数据进行转码
- 充分利用CSP(Content-Security-Policy)，限制其他源的资源下载，禁止像第三方域提交数据，禁止执行内联脚本和未授权的脚本，即使上报
- 使用 HttpOnly 属性，HTTP的响应头，set-cookied: xxxx ; HttpOnly。只能通过HTTP请求使用，无法通过document.cookie获取
还可以通过添加验证码的方式防止脚本冒充用户进行提交危险操作，对于一些不信任的输入，还可以限制其输入长度

## CSRF攻击：陌生链接不要随便点
## 是什么
CSRF(Cross-site request forgery)，跨站请求伪造。攻击者引诱用户打开恶意网站，利用用户的登录状态，发起跨站请求做坏事。
- 自动发起GET请求，如将请求接口隐藏在图片中：
```
<img src="http://www.abc.cn/sendcoin?user=xxx&number=10"/>
```
- 自动发起POST请求，一个隐藏的表单
- 引诱用户点击链接
- CSRF Token

### 怎么办
- 对于响应头的Cookie设置，SameSite可以设置Strict、Lax 和 None
- 验证请求的来源站点，通过Origin，Referer判断
