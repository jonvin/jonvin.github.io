---
title: vite和react
date: 2022-04-26 10:15:13
tags:
---

## 使用 vite 创建项目脚手架

执行 yarn create vite react_with_vite --template react-ts 命令， 执行过程如下：

```cmd
$ yarn create vite react_with_vite --template react-ts
yarn create v1.22.17
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...

success Installed "create-vite@2.8.0" with binaries:
      - create-vite
      - cva

Scaffolding project in /Users/apple/workspace/test/vite/react_with_vite...

Done. Now run:

  cd react_with_vite
  yarn
  yarn dev

✨  Done in 7.62s.
```

## 规划开发目录

● 新建/src/assets 目录用于存放静态资源
● 新建/src/pages 目录用于存放页面级组件
● 新建/src/components 目录用于存放组件元素

## 添加 react 路由依赖管理

yarn add react-router-dom@6
了解更多可以查看[官方文档](https://reactrouter.com/docs/en/v6/getting-started/overview)

## react-router-dom v6 的使用

1.编辑/main.tsx 内容如下

```JavaScript

import React from 'react'
import ReactDOM from 'react-dom/client'
import {
  BrowserRouter,
  Routes,
  Route
} from "react-router-dom";

import './index.css'

import App from './App'
import Home from './pages/home/home';
import Login from './pages/login/login';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<App />}>
          <Route path="login" element={<Login />}></Route>
          <Route path="home" element={<Home />}></Route>
        </Route>
        <Route path="/home" element={<Home />} />
        <Route path="/login" element={<Login />} />
      </Routes>
    </BrowserRouter>
  </React.StrictMode>
)

```

## vite 移动端适配方案

1.安装 amfe-flexible 可以进行 HTML 根节点的初始化、屏幕自适应

```cmd
    yarn add amfe-flexible
```

2.在 main.js 中引入 amfe-flexible

```main.js
    import "amfe-flexible";
```

3.安装 postcss-pxtorem 实现将 px 转成 rem。

```cmd
   yarn add postcss-pxtorem
```

4.vite.config.ts 中配置 postcss-pxtorem

```JavaScript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import postCssPxToRem from 'postcss-pxtorem'

export default defineConfig({
  plugins: [react()],
  css: {
    postcss: {
      plugins: [
        postCssPxToRem({
          rootValue: 75,  // 1rem 的大小
          rootValuePC: 192, // PC 端文件字体大小
          propList: ['*'],  // 需要转换的属性，这里全部进行转换
          mediaQuery: false, // 是否在媒体查询中转换
          // exclude: "/src/pages/login"
        })
      ]
    }
  },
  server: {
    strictPort: true
  }
})
```

## vite 配置代理解决跨域问题

1.server.proxy 更多配置请查看[官方文档](https://vitejs.cn/config/#server-proxy)

```JavaScript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import postCssPxToRem from 'postcss-pxtorem'

export default defineConfig({
  plugins: [react()],
  css: {
    postcss: {
      plugins: [
        postCssPxToRem({
          rootValue: 75,  // 1rem 的大小
          rootValuePC: 192, // PC 端文件字体大小
          propList: ['*'],  // 需要转换的属性，这里全部进行转换
          mediaQuery: false, // 是否在媒体查询中转换
          // exclude: "/src/pages/login"
        })
      ]
    }
  },
  server: {
    strictPort: true,
    proxy: {
        // 选项写法
        '/api': {
            target: 'http://jsonplaceholder.typicode.com', // 所要代理的目标地址
            changeOrigin: true, // 默认为 false 将请求源头改为 target 配置的地址
            rewrite: (path) => path.replace(/^\/api/, '') // 重写传过来的path路径 比如`/api/index/1?id=1&name=jonwen` 注意：path路径最前面有斜杆（/）因此正则匹配的时候不要忘了是斜杆（/）开头的：选项的key 也是斜（/）杆开头的
        },
    }
  }
})
```

## vite 配置@为跟目录

1.在项目中添加 path

```cmd
yarn add path
```

2.在 tsconfig.json 中加上 "baseUrl": ".", "paths": {"@/_": ["src/_"]}

```tsconfig.json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "allowJs": false,
    "skipLibCheck": true,
    "esModuleInterop": false,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

3.修改 vite.config.ts 配置

```JavaScript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import postCssPxToRem from 'postcss-pxtorem'
import { resolve } from 'path'

export default defineConfig({
  plugins: [react()],
  base: "./",  // 打包路径
  // 配置@根目录方法一
  // resolve: {
  //   alias: {
  //     '@': resolve(__dirname, './src')
  //   }
  // }
  // 配置@根目录方法二
  resolve: {
    alias: [ //配置别名
      { find:'@', replacement: resolve(__dirname, './src') }
    ]
  },
  css: {
    postcss: {
      plugins: [
        postCssPxToRem({
          rootValue: 75,  // 1rem 的大小
          rootValuePC: 192, // PC 端文件字体大小
          propList: ['*'],  // 需要转换的属性，这里全部进行转换
          mediaQuery: false, // 是否在媒体查询中转换
          // exclude: "/src/pages/login"
        })
      ]
    }
  },
  server: {
    strictPort: true,
    proxy: {
        // 选项写法
        '/api': {
            target: 'http://jsonplaceholder.typicode.com', // 所要代理的目标地址
            changeOrigin: true, // 默认为 false 将请求源头改为 target 配置的地址
            rewrite: (path) => path.replace(/^\/api/, '') // 重写传过来的path路径 比如`/api/index/1?id=1&name=jonwen` 注意：path路径最前面有斜杆（/）因此正则匹配的时候不要忘了是斜杆（/）开头的：选项的key 也是斜（/）杆开头的
        },
    }
  }
})

```

## vite 配置 less

1.添加 less

添加 less 依赖即可，yarn add less

2.修改 vite.config.ts

```JavaScript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import postCssPxToRem from 'postcss-pxtorem'
import { resolve } from 'path'

export default defineConfig({
  plugins: [react()],
  base: "./",  // 打包路径
  // 配置@根目录方法一
  // resolve: {
  //   alias: {
  //     '@': resolve(__dirname, './src')
  //   }
  // }
  // 配置@根目录方法二
  resolve: {
    alias: [ //配置别名
      { find:'@', replacement: resolve(__dirname, './src') }
    ]
  },
  css: {
    preprocessorOptions: {
      less: {
        javascriptEnabled: true, // 设置为true才能引入高版本的less
        modifyVars: {
          // 此处也可以设置变宽色、字体大小等
          'primary-color': '#e30015'  // 主题色设置联通红
        }
      }
    },
    postcss: {
      plugins: [
        postCssPxToRem({
          rootValue: 75,  // 1rem 的大小
          rootValuePC: 192, // PC 端文件字体大小
          propList: ['*'],  // 需要转换的属性，这里全部进行转换
          mediaQuery: false, // 是否在媒体查询中转换
          // exclude: "/src/pages/login"
        })
      ]
    }
  },
  server: {
    strictPort: true,
    proxy: {
        // 选项写法
        '/api': {
            target: 'http://jsonplaceholder.typicode.com', // 所要代理的目标地址
            changeOrigin: true, // 默认为 false 将请求源头改为 target 配置的地址
            rewrite: (path) => path.replace(/^\/api/, '') // 重写传过来的path路径 比如`/api/index/1?id=1&name=jonwen` 注意：path路径最前面有斜杆（/）因此正则匹配的时候不要忘了是斜杆（/）开头的：选项的key 也是斜（/）杆开头的
        },
    }
  }
})

```

3.在 main.js 中引入对应的 less 组件

```js
import "antd/dist/antd.less";
```
