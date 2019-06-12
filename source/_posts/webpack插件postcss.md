---
title: webpack插件postcss
date: 2019-06-12 10:20:05
tags:
---
前言：前一阵子对公司的h5页面进行了改版重构，在使用webpack的过程中了解了很多非常好用的工具，postcss 就是 其中之一.通过翻阅大量的书籍，浏览了很多大神的博客，我觉得我也有必要写点什么了。因为站在巨人的肩膀上，所以才能看得更远！

### 什么是postcss 

![图](https://github.com/jonvin/jonvin.github.io/blob/master/img/writeImg/postcss.png)
postcss 一种对css编译的工具，类似babel对js的处理，postcss 只是一个工具，本身不会对css 内容进行操作，它通过插件实现功能，autoprefixer 就是其一。常见的功能如：

1.使用下一代css语法 (css4 语法)

2.自动补全浏览器前缀

3.自动把px代为转换成 rem

4.css 代码压缩等



### 如何在webpack中使用


1.安装

``` bash
cnpm install postcss-loader --save-dev
```


2.在webpack中配置

一般与其他loader配合使用。

``` bash

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        // 此处为使用postcss分离css的写法,这里还使用了webpack的ExtractTextPlugin插件来进行分离
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: ["css-loader", "postcss-loader"],
          // css中的基础路径
          publicPath: "../"          
        })

      }
    ]
  },
  plugins: [
    new ExtractTextPlugin({
        filename: 'css/[name].min.css',
    }),
  ]
}

```
More info: [ExtractTextPlugin](https://www.npmjs.com/package/extract-text-webpack-plugin)


3.postcss配置

项目根目录新建 postcss.config.js 文件，所有使用到的插件都需在这里配置，空配置项时配置xx:{}
``` bash
module.exports = {
  plugins: {
    'autoprefixer': {
        browsers: '> 5%' //可以都不填，用默认配置
    }
  }
}
```



### 常用的postcss插件
1.autoprefixer
前缀补全，全自动的，无需多说

安装：cnpm install autoprefixer --save-dev


2.postcss-cssnext
使用下个版本的css语法，语法见下文 [cssnext (css4)语法](#cssnext)

安装：cnpm install postcss-cssnext --save-dev
cssnext包含了 autoprefixer ，如果都安装会报错，如下：

``` bash
Warning: postcss-cssnext found a duplicate plugin ('autoprefixer') in your postcss plugins. This might be inefficient. You should remove 'autoprefixer' from your postcss plugin list since it's already included by postcss-cssnext.
```


3.postcss-pxtorem

安装：cnpm install postcss-pxtorem --save-dev

``` bash
"postcss-pxtorem":{
            rootValue: 30, //你在html节点设的font-size大小
            unitPrecision: 5,  //转rem精确到小数点多少位
            propList: ['font', 'font-size', 'line-height', 'letter-spacing'],//指定转换成rem的属性，支持 * ！ *代表所有的都转换成rem
            selectorBlackList: [], // str/reg 指定不转换的选择器，str时包含字段即匹配
            replace: true,
            mediaQuery: false, //媒体查询内的px是否转换
            minPixelValue: 5 //小于指定数值的px不转换
        },
```
特殊技巧:不转换成rem

px检测区分大小写，也就是说Px/PX/pX不会被转换，可以用这个方式避免转换成rem



<h3 id="cssnext">cssnext (css4)语法</h3>

cssnext 和 css4 并不是一个东西，cssnext使用下个版本css的草案语法

相当于一个变量，变量的好处显而易见，重复使用


1 . 定义

``` bash
:root {
    --lehaColor: #37cc75; 
    --lehaText:#999;
    --mainColor: #66D066;
    --orangeColor: #f90;
    --blueColor: #49f;
    --greyColor: #f8f8f8;
    --mainBackground: #F2F6F8;
    --subBackground: #F8F8F8;
    --infoBackground: #F4F4F4;
    --borderColor: #E2E2E2;
    --fontColor: #333;
    --fontInfoColor:#999;
    --fontSubColor: #666;
    --fontBold: 700;
    --borderBottom: {
        border:0 ;
        border-bottom:1px solid ;
        border-image: svg(1px-border param(--color #e2e2e2)) 1 stretch;
    };
    --borderTop: {
        border:0 ;
        border-top: 1px solid ; 
        border-image: svg(1px-border param(--color #e2e2e2)) 1 stretch;
    };
    --borderLeft: {
        border:0 ;
        border-left: 1px solid ; 
        border-image: svg(1px-border param(--color #e2e2e2)) 1 stretch;
    };
    --borderRight: {
        border:0 ;
        border-right: 1px solid ; 
        border-image: svg(1px-border param(--color #e2e2e2)) 1 stretch;
    };
    --borderBottomRight: {
        border:0 ;
        border-bottom:1px solid ;
        border-right: 1px solid ; 
        border-image: svg(1px-border param(--color #e2e2e2)) 1 stretch;
    };
    --border: {
        border: 1px solid ; 
        border-image: svg(1px-border param(--color #e2e2e2)) 1 stretch;
    };
    --backgroundBottom: {
        background-image: -moz-linear-gradient(var(--mainColor) 100%, transparent 100%);
        background-image: -o-linear-gradient(var(--mainColor) 100%, transparent 100%);
        background-image: linear-gradient(var(--mainColor) 100%, transparent 100%);
        background-repeat: no-repeat;
        background-position: bottom;
    };
}
```


2 . 使用 

使用 var(xx) 调用自定义属性

``` bash
.test{
    background: var(--mianColor);
}

/*编译后*/
.test{
    background: #ffc001;
}
```


3. 大小计算

一般用于px rem等的计算

语法：cale(四则运算)

``` bash
/*css3*/
.test {
    width: calc(100% - 50px);
}
/*css4 允许变量*/
:root {
  --fontSize: 1rem;
}
h1 {
    font-size: calc(var(--fontSize) * 2);
}

/*编译后*/
.test{
    font-size: 32px;
    font-size: 2rem;
}
```


4.自定义定义媒体查询

1 . 定义

语法 @custom-media xx (条件) and (条件)

``` bash
@custom-media --small-viewport (max-width: 30rem);
```

另外css4 可以使用>= 和 <= 代替min-width 、max-width

``` bash
@media (width >= 500px) and (width <= 1200px) {

}
@media (--small-viewport) {

}

/*编译后*/
@media (min-width: 500px) and (max-width: 1200px) {

}
@media (max-width: 30rem) {

}
```