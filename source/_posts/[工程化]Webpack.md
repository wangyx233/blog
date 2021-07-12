---
title: 【工程化】Webpack
categories: ['Webpack']
toc: true
---

# 历史
## 为什么需要
1. 转换ES6
2. 转换JSX
3. 压缩混淆
4. CSS预处理器
5. 图片压缩
...

gulp 任务打包器，文件流的概念，每一步构建的结果不会存本地磁盘，而是存在内存里，可以加快打包速度。

webpack vs gulp 社区生态丰富，配置灵活，插件化扩展，官方更新迭代快；module builder vs task runner

# 简介
配置文件，默认：webpack.config.js
可以使用 webpack --config xxx 指定配置文件

webpack
webpack-cli
### 核心概念

1. entry：模块依赖打包的入口，单页应用为字符串，多页应用为{key: value}
2. output：将编译的文件输出到磁盘，通过filename [xxx]区别多入口多输出，输出的文件名称，为entry设置的key名
3. loaders！webpack开箱即用的只有js和json两种类型，所以需要通过loaders去支持其他文件类型并转成有效的模块，并且可以加到依赖图里
loaders其实是个函数，接受源文件为参数，返回转换的结果。链式调用的，从右向左执行
比如：
- babel-loader
- css-loader
- less-loader
- file-loader
4. plugins，增强webpack的功能，对打包文件的优化，资源的管理，环境变量的注入。作用于整个构建过程
- CommonsChunkPlugin
- HtmlWebpackPlugin
- UglifyjsWebpackPlugin
5. mode: 指定打包的环境，production，development

```
const path = require('path');

module.exports = {
    entry: {
        app: 'xxx',
        admin: 'sss'
    },
    output: {
        filename: '[xxx].js',
        path: ''
    }
    mode: 'production'
}

```

6. 文件监听的原理分析
轮询地判断文件最后编辑时间是否变化，某个文件发生了变化，并不会立刻告诉监听者，而是先缓存起来，等aggregateTimeout
```
watchOptions: {
    ignored: /node_modules/,
    aggregateTimeout: 300, // 监听到变化后，多长时间后再去执行
    poll: 1000 // 1s询问多少次
}
```
7. 热更新
webpack compile
HMR Server   -> HMR Runtime，会被注入到浏览器的bundle.js里面，浏览器里的bundle.js 就可以建立连接websocket，更新变化
Bundle Server -> 浏览器访问 localhost
- 两个阶段，启动阶段 文件系统 -> webpack compile -> Bundle Server
- 变更阶段，文件系统 -> webpack compile -> HMR Server -> HMR Runtime （json形式）

8. 文件指纹 - 打包后输出的文件名后缀
怎样生成的？
- hash，和整个项目的构建有关，只要项目文件有修改，整个项目构建的hash就会更改
- chunkhash，和webpack打包的chunk有关，不同的entry会生成不同的chunkhash
- contenthash，根据文件内容来生成hash，文件内容不变，则contenthash不变

ps. file-loader 里面的hash也是指contenthash的意思。style-loader是将css代码插入到head中,MiniCssExtractPlugin是将css文件提取出独立的文件，二者互斥

9. 清理构建目录 - clean-webpack-plugin
10. css
- 属性前缀 - autoprefixer，postcss-loader
- px rem 相互转换 rem: font-size of the root element。px2rem-loader

10. 静态资源内联，raw-loader。读取文件的内容，然后把文件的内容插入到对应的位置

11. 多页面打包，MPA - 借助 glob
> glob.sync(path.join(__dirname, './src/*/index.js'))
动态获取entry以及HtmlWebpackPlugin

12. source-map定位到源代码，开发环境中用，线上环境关闭
cheap-source-map , inline-source-map，eval 不会有单独的map文件

13. 提取公共资源
- HtmlWebpackExternalsPlugin，放cdn的地址
- SplitChunkPlugin

14. Tree Shaking
设置mode: production,是默认开启的
- 原理：DCE（Dead Code  Elimination） 
 代码不可到达的，不能执行；执行结果不会使用；代码只写不用
- 利用ES6模块的特点，只能作为模块顶部的语句出现；import的模块名只能是字符串常量；import binding是不可变的
- uglify阶段删除无用代码
- 具有副作用的代码，tree shaking会失效

副作用：一个函数会、或者可能会对函数外部变量产生影响的行为

15. Scope hoisting
问题：
 - 大量函数闭包包裹代码，导致体积增大
 - 运行代码时创建的函数作用域多，内存开销大

 模块转换，一个闭包包裹；import被转换成__webpack_require

16. 代码分割，动态import，通过JSONP实现
- 抽离相同代码到一个共享块
- 脚本懒加载，初始下载的代码更小 