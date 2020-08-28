---
title: vue-cli反向代理解决跨域以及axios的配置
date: 2020-08-27 09:59:48
tags:
---
前言： 最近忙于各种各样大大小小的项目，都没时间写博客了。现在习惯于大型项目用react, 小型项目用 vue，本期就写一篇在漫漫学习之路中 小型项目中之前使用vue-cli 所遇到的问题吧！

### vue跨域实现与原理（proxyTable）

* 在使用vue-cli搭建的项目在本地与后端联调时,是因为开发中使用node 运行服务器，IP与后端不一致，所以会产生跨域问题，需要使用JSONP、跨域代理等手段进行跨域请求，而在vue-cli中有一个强大的插件（http-proxy-middleware） 帮我们解决了这个问题，我们在开发中只需要简单地配置一下就可以了！


* 我们在vue-cli 的根目录下找到 （config--> index.js）


配置前如下

``` index.js 
  dev: {

    // Paths
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {},

    // Various Dev Server settings
    host: 'localhost', // can be overwritten by process.env.HOST
    port: 8080, // can be overwritten by process.env.PORT, if port is in use, a free one will be determined
    autoOpenBrowser: true,
    errorOverlay: true,
    notifyOnErrors: true,
    poll: false, // https://webpack.js.org/configuration/dev-server/#devserver-watchoptions-    
    ....
  }
```



找到 dev 中的 proxyTable 配置后如下

``` index.js
  dev: {

    // Paths
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {
      '/api': {
        target: 'https://i.***.com/', // 接口的域名
        secure: true,  // 如果是https接口，需要配置这个参数
        changeOrigin: true, // 如果接口跨域，需要进行这个参数配置，为true的话，请求的header将会设置为匹配目标服务器的规则（Access-Control-Allow-Origin）
        pathRewrite: {
          '^/api': '/' //本身的接口地址没有 '/api' 这种通用前缀，所以要rewrite，如果本身有则去掉 
        }
      }      
    },

    // Various Dev Server settings
    host: 'localhost', // can be overwritten by process.env.HOST
    port: 8080, // can be overwritten by process.env.PORT, if port is in use, a free one will be determined
    autoOpenBrowser: true,
    errorOverlay: true,
    notifyOnErrors: true,
    poll: false, // https://webpack.js.org/configuration/dev-server/#devserver-watchoptions-
    ....
  }
```

使用如下

``` index.vue
// 代理前的接口地址 https://i.***.com/xinli/xcx/index
// 代理后的接口地址相当于是 http://localhost:8080/api/xinli/xcx/index
// 注意事项： 以上面代码设置的为例，会把请求中所有带有/api字段的都替换掉，例如api/xinli/api，前后两个都会被替换，导致404等错误，在代理数量比较多的时候容易出现这个问题。

 this.$http.get("/api/xinli/xcx/index", {
 }
 .then(res => {
 })
 .catch(function(error) {
 });   


```

* proxyTable原理总结 

  浏览器是禁止跨域的，但是服务端不禁止，在本地运行npm run dev等命令时实际上是用node运行了一个服务器，因此proxyTable实际上是将请求发给自己的服务器，再由服务器转发给后台服务器，做了亦曾代理，因为不会出现跨域问题。


### axios的配置和封装

* 在写这个之前，结合了自己的项目，还看了各路大神的博客，正所谓八仙过海各显神通。我觉得有的时候不一定要封装得最好，但是一定要封装得最适合自己的项目，尽量避免一些冗余的代码，因为每个人项目的大小个需求都不一样。我用vue 做的是小的项目，所以这里我对axios　是做了一个非常简单的封装。

首先在vue-cli根目录 的src 里面 创建一个axios 文件夹该文件夹下创建一个 request.js 

``` request.js
import axios from 'axios'
import qs from 'qs'
// import { Toast } from 'mint-ui'

// 创建 axios 实例 

let service = axios.create({
  timeout: 5000,
  headers: {
    'content-type': 'application/json',
    
  }
})
axios.defaults.transformRequest = data => qs.stringify(data)

// 添加请求拦截器
// http request 拦截器


service.interceptors.request.use(
  config => {
    const token = sessionStorage.getItem('token')
    if (token ) { // 判断是否存在token，如果存在的话，则每个http header都加上token
      config.headers.authorization = token  //请求头加上token
    }
    return config
  },
  err => {
    return Promise.reject(err)
  })


// 添加响应拦截器

service.interceptors.response.use(function (response) {
    // 对响应数据做点什么 这里的话可以根据不同项目 自己的需求去写。
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });

export default{
  service,
  get (url, data) {
    console.log('data', data)
    return service({
      url: url,
      method: 'get',
      params: data
    })
  },
  post (url, data) {
    return service({
      url: url,
      method: 'post',
      data
    })
  }
}



```