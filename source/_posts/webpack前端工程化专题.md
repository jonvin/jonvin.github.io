---
title: webpack前端工程化专题
date: 2021-04-25 09:40:25
tags:
---

声明：本篇文章纯属笔记性文章，非整体原创，是对webpack知识的整理，对自己有很大帮助才分享出来


### webpack的作用

1、依赖管理：方便引用第三方模块、让模块更容易复用，避免全局注入导致的冲突、避免重复加载或者加载不需要的模块。会一层一层的读取依赖的模块，添加不同的入口；同时，不会重复打包依赖的模块。

2、合并代码：把各个分散的模块集中打包成大文件，减少HTTP的请求链接数，配合UglifyJS（压缩代码）可以减少、优化代码的体积。

3、各路插件：统一处理引入的插件，babel编译ES6文件，TypeScript,eslint 可以检查编译期的错误。

一句话总结：webpack 的作用就是处理依赖，模块化，打包压缩文件，管理插件。
一切皆为模块，由于webpack只支持js文件，所以需要用loader 转换为webpack支持的模块，其中plugin 用于扩张webpack 的功能，在webpack构建生命周期的过程中，在合适的时机做了合适的事情。


### webpack怎么工作的过程

1、解析配置参数，合并从shell(npm install 类似的命令)和webpack.config.js文件的配置信息，输出最终的配置信息；

2、注册配置中的插件,让插件监听webpack构建生命周期中的事件节点，做出对应的反应；

3、解析配置文件中的entry入口文件，并找出每个文件依赖的文件，递归下去；

4、在递归每个文件的过程中，根据文件类型和配置文件中的loader找出对应的loader对文件进行转换；

5、递归结束后得到每个文件最终的结果，根据entry 配置生成代码chunk(打包之后的名字)；

6、输出所以chunk 到文件系统。    


### webpack是什么？

1.一种前端构建工具,一个静态模块的打包器

2.前端所有资源文件都会作为模块处理

3.它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源


### webpack 五个核心概念分别是什么？

1.Entry 

入口（Entry） 指示Webpack 以哪个文件为入口起点开始打包，分析内部构建依赖图

2.output

输出（output） 指示webpack 打包后的资源bundles 输出到哪里去，以及如何命名

3.Loader

loader 能让webpack 处理 非javascript/json 文件 (webpack 自身只能处理javascript)

4.plugins

插件(Plugins) 可以用于执行范围更广的任务，包括从打包优化和压缩到重新定义环境的变量

5.Mode

模式（mode）指示Webpack 使用相应模式的配置, 只有development (开发环境) 和 production (生产环境) 两种模式


### webpack的热更新是如何做到的？说明其原理？

webpack的热更新又称热替换（Hot Module Replacement），缩写为HMR。 

这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。


1.第一步，在 webpack 的 watch 模式下，文件系统中某一个文件发生修改，webpack 监听到文件变化，
根据配置文件对模块重新编译打包，并将打包后的代码通过简单的 JavaScript 对象保存在内存中。

2.第二步，是webpack-dev-server 和 webpack之间的接口交互，而在这一步，主要是dev-server 的中间件 webpack-dev-middleware 和 webpack 之间的交互， 
webpack-dev-middleware 调用webpack 暴露API 对代码变化进行监控，并且告诉webpack 将代码打包到内存中。

3.第三步是 webpack-dev-server 对文件变化的一个监控，这一步不同于第一步，并不是监控代码变化重新打包。
当我们在配置文件中配置了devServer.watchContentBase 为true的时候 Server 会监听这些配置文件夹中静态文件的变化，
变化后会通知浏览器端对应用进行live reload。注意这里是浏览器刷新和HMR是两个概念。

4.第四步也是 webpack-dev-server代码的工作，该步骤主要是通过sockjs (webpack-dev-server 依赖) 在浏览器和服务端之间建立一个 websocket 长连接，将 webpack 编译打包的各个阶段的状态信息告知浏览器端，同时也包括第三步中 Server 监听静态文件变化的信息。浏览器端根据这些 socket 消息进行不同的操作。当然服务端传递的最主要信息还是新模块的 hash 值，后面的步骤根据这一 hash 值来进行模块热替换。

5.webpack-dev-server/client 端并不能够请求更新的代码，也不会执行热更模块操作，而把这些工作又交回给了 webpack，webpack/hot/dev-server 的工作就是根据 webpack-dev-server/client 传给它的信息以及 dev-server 的配置决定是刷新浏览器呢还是进行模块热更新。当然如果仅仅是刷新浏览器，也就没有后面那些步骤了。

6.HotModuleReplacement.runtime 是客户端 HMR 的中枢，它接收到上一步传递给他的新模块的 hash 值，它通过 JsonpMainTemplate.runtime 向 server 端发送 Ajax 请求，服务端返回一个 json，该 json 包含了所有要更新的模块的 hash 值，获取到更新列表后，该模块再次通过 jsonp 请求，获取到最新的模块代码。

7.而第 10 步是决定 HMR 成功与否的关键步骤，在该步骤中，HotModulePlugin 将会对新旧模块进行对比，决定是否更新模块，在决定更新模块后，检查模块之间的依赖关系，更新模块的同时更新模块间的依赖引用

8.最后一步，当 HMR 失败后，回退到 live reload 操作，也就是进行浏览器刷新来获取最新打包代码。


### 如何利用webpack来优化前端性能？（提高性能和体验）

用webpack优化前端性能是指优化webpack的输出结果，让打包的最终结果在浏览器运行快速高效。

压缩代码。删除多余的代码、注释、简化代码的写法等等方式。可以利用webpack的UglifyJsPlugin和ParallelUglifyPlugin来压缩JS文件， 利用cssnano（css-loader?minimize）来压缩css
利用CDN加速。在构建过程中，将引用的静态资源路径修改为CDN上对应的路径。可以利用webpack对于output参数和各loader的publicPath参数来修改资源路径
删除死代码（Tree Shaking）。将代码中永远不会走到的片段删除掉。可以通过在启动webpack时追加参数--optimize-minimize来实现
提取公共代码。


### 如何提高webpack的构建速度？

1.多入口情况下，使用CommonsChunkPlugin来提取公共代码
2.通过externals配置来提取常用库
3.利用DllPlugin和DllReferencePlugin预编译资源模块 通过DllPlugin来对那些我们引用但是绝对不会修改的npm包来进行预编译，
再通过DllReferencePlugin将预编译的模块加载进来。
4.使用Happypack 实现多线程加速编译
5.使用webpack-uglify-parallel来提升uglifyPlugin的压缩速度。 原理上webpack-uglify-parallel采用了多核并行压缩来提升压缩速度
6.使用Tree-shaking和Scope Hoisting来剔除多余代码


### 怎么配置单页应用？怎么配置多页应用？

单页应用可以理解为webpack的标准模式，直接在entry中指定单页应用的入口即可，这里不再赘述

多页应用的话，可以使用webpack的 AutoWebPlugin来完成简单自动化的构建，但是前提是项目的目录结构必须遵守他预设的规范。 多页应用中要注意的是：

每个页面都有公共的代码，可以将这些代码抽离出来，避免重复的加载。比如，每个页面都引用了同一套css样式表
随着业务的不断扩展，页面可能会不断的追加，所以一定要让入口的配置足够灵活，避免每次添加新页面还需要修改构建配置


### 如何在vue项目中实现按需加载？

Vue UI组件库的按需加载 为了快速开发前端项目，经常会引入现成的UI组件库如ElementUI、iView等，但是他们的体积和他们所提供的功能一样，是很庞大的。 而通常情况下，我们仅仅需要少量的几个组件就足够了，但是我们却将庞大的组件库打包到我们的源码中，造成了不必要的开销。


### 是否写过Loader和Plugin？描述一下编写loader或plugin的思路？

Loader像一个"翻译官"把读到的源文件内容转义成新的文件内容，并且每个Loader通过链式操作，将源文件一步步翻译成想要的样子。

编写Loader时要遵循单一原则，每个Loader只做一种"转义"工作。 每个Loader的拿到的是源文件内容（source），可以通过返回值的方式将处理后的内容输出，也可以调用this.callback()方法，将内容返回给webpack。 还可以通过 this.async()生成一个callback函数，再用这个callback将处理后的内容输出出去。 此外webpack还为开发者准备了开发loader的工具函数集——loader-utils。

相对于Loader而言，Plugin的编写就灵活了许多。 webpack在运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。



### 如何在vue项目中实现按需加载？

Vue UI组件库的按需加载 为了快速开发前端项目，经常会引入现成的UI组件库如ElementUI、iView等，但是他们的体积和他们所提供的功能一样，是很庞大的。 而通常情况下，我们仅仅需要少量的几个组件就足够了，但是我们却将庞大的组件库打包到我们的源码中，造成了不必要的开销。

不过很多组件库已经提供了现成的解决方案，如Element出品的babel-plugin-component和AntDesign出品的babel-plugin-import 安装以上插件后，在.babelrc配置中或babel-loader的参数中进行设置，即可实现组件按需加载了。

``` js
{
  "presets": [["es2015", { "modules": false }]],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}

```
单页应用的按需加载 现在很多前端项目都是通过单页应用的方式开发的，但是随着业务的不断扩展，会面临一个严峻的问题——首次加载的代码量会越来越多，影响用户的体验。

通过import(*)语句来控制加载时机，webpack内置了对于import(*)的解析，会将import(*)中引入的模块作为一个新的入口在生成一个chunk。 当代码执行到import(*)语句时，会去加载Chunk对应生成的文件。import()会返回一个Promise对象，所以为了让浏览器支持，需要事先注入Promise polyfill


