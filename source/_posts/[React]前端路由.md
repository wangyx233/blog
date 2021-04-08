---
title: 【React】前端路由-React Router
categories: ["React"]
tags: ["React"]
toc: true
---

# What
- 切换Hash
- 切换Path
HTML5的History API中的 pushState、replaceState，还需要在服务端配置historyApiFallback
路由守卫
# How
1. 路由器：BrowserRouter 和 HashRouter
- BrowserRouter 是使用 HTML 5 的 history API 来控制路由跳转的。
- HashRouter 是通过 URL 的 hash 属性来控制路由跳转的。
```
window.location.hash
// 监听hash变化，点击浏览器的前进后退会触发
window.addEventListener('hashchange', function(event){
    // 根据 hash 的变化更新内容
},false)
window.history.forward()  // 前进到下一页
window.history.back() // 后退到上一页
window.history.go(2)
history.pushState(data[,title][,url]); // 向浏览历史中追加一条记录
history.replaceState(data[,title][,url]); // 修改（替换）当前页在浏览历史中的信息
window.addEventListener('popstate', function(e) {
  console.log(e)
});
```

> go、forward 和 back 等方法的调用确实会触发 popstate，但是pushState 和 replaceState 不会。不过这一点问题不大，我们可以通过自定义事件和全局事件总线来手动触发事件。