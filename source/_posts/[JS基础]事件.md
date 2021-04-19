---
title: 【JS基础】事件
categories: ['JavaScript']
toc: true
---

- 事件冒泡的流程
- 通用的事件监听
- 无限下拉的图片列表，如何监听每个图片的点击

# 知识点
- 事件绑定
- 事件冒泡
- 事件代理

1. 事件绑定，事件冒泡
```
function bindEvent(elem, type, selecotr, fn) {
    if(fn == null) {
        fn = selector;
        selector = null;
    }

    elem.addEventListener(type, e => {
        let target;
        if(selector) { // 需要代理
            target = e.target;
            if(target.matches(selecotr)) { // 判断是否是触发元素
                fn.call(target, e)
            }
        }else {
            fn.call(target, e);
        }
    });
}
const btn = document.getElementById('btn');
bindEvent(btn, 'click', event => {
    console.log(event.target) // 获取触发的元素 
    event.preventDefault() // 取消默认事件
    event.stopPropagation() // 阻止冒泡
})
```

2. 事件代理
   - 代码简洁，事件绑定到触发的父元素上
   - 减少浏览器内存占用
   - 不要滥用