---
title: 【CSS】梳理
categories: ['CSS']
toc: true
---

- 布局
- 定位
- 图文样式
- 响应式
- CSS3 flex 动画

1. 盒模型，宽度
2. margin纵向重叠问题
3. margin负值
4. BFC理解应用！
5. float布局 clearfix
6. flex画骰子
7. absolute relative
8. 居中对齐
9. line-height 继承
10. rem是什么
11. 如何实现响应式
12. CSS3动画


1. 盒模型
offsetWidth
box-sizing: content-box; 默认值
box-sizing: border-box; 

2. margin 纵向重叠：相邻元素的margin-top 和 margin-bottom会相互重叠。空内容的P标签也会重叠

3. margin 负值
margin-left，margin-top 向相反方向移动
margin-right，自身不受影响，右侧元素左移
margin-bottom，自身不受影响，下方的会上移
空的标签也会重叠

4. BFC
> 块级格式化上下文。Block Format Content，是一个独立的渲染区域，内部元素的渲染不会影响边界以外的元素。
形成BFC的条件：
- float 不是 none
- position是absolute或者fixed
- overflow不是visible
- display是flex或者inline-block

常见应用：
- 清除浮动

5. 圣杯/双飞翼
圣杯：
三栏布局，两侧内容固定，中间内容宽度自适应
- 使用float
- 两侧使用margin负值，确定左右两块
- 父元素使用padding

双飞翼：main  left right 顺序
- 使用float
- 使用margin为左右留白，margin-left

6. 手写clearfix

.clearfix:after {
    content: '';
    display: 'table';
    clear: both;
}

7. flex
三色的骰子
align-self

8. 定位
absolute：最近一层的非static的
relative：自身定位
居中对齐的方式：
- 水平
1. inline: text-align: center
2. block: margin: auto
3. absolute: left: 50% + margin-left: 负值
- 垂直
1. inline: line-height
2. absolute: top: 50% + margin-top: 负值 或者  top: 50%; left: 50%; transform: translate(-50%, -50%) 或者 top,right,bottom,left:0 + margin: auto

9. line-height 如何继承
- 如果是具体的数值，直接继承
- 如果是数字比例，1，1.5，直接继承这个数值
- 如果是百分比，则继承计算出的值，算出line-height的数值
和字体的乘积

10. 响应式
- rem：相对根元素html的长度单位，em相对父元素的长度单位 
html {
    font-size: 100px;
}
任何宽度的都可以使用这个rem，width，font-size等
- media-query 设置根元素的font-size，然后设置rem
- vw / vh 网页视口尺寸 
window.screen.height //屏幕
window.innerHeight //视口 100vh 100vw vmax vmin 取vh vw的最大/小
document.body.clientHeight //body
rem的特性：阶梯型，不够精细

