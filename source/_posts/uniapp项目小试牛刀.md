---
title: '[uniapp项目小试牛刀]'
date: 2019-10-24 17:49:07
tags: uniapp
---


前言:3天开发上线了一个简单的 uniapp 项目 因为模板语法跟 Vue 90%的相似度所以开发起来还是比较顺畅,在开发的过程中也踩过了一些小的坑。


## 目录结构

``` bash
┌─components                                              uni-app组件目录
│  └─comp.vue                                             可复用的组件
├─hybrid                                                  存放本地网页的目录
├─platforms                                               存放各平台专用页面的目录
├─pages                                                   业务页面文件存放的目录
│  ├─articleContent
│  │  └─articleContent.vue                                articleContent页面
│  ├─rticlelist
│  │   └─articlelist.vue                                  list页面
│  └─index
│     └─index.vue                                         首页
├─static                                                  存放应用引用静态资源（如图片、视频等）的目录，注意：静态资源只能存放于此
├─wxcomponents                                            存放小程序组件的目录
├─main.js                                                 Vue初始化入口文件
├─App.vue                                                 应用配置，用来配置App全局样式以及监听 应用生命周期
├─manifest.json                                           配置应用名称、appid、logo、版本等打包信息
└─pages.json                                              配置页面路由、导航条、选项卡等页面类信息
```


## 第三方插件 Parser

- [富文本插件](https://github.com/jin-yufeng/Parser)	
- REMARK： Parser是对  uniapp 的 rich-text  组件进行了二次封装  [详见](https://uniapp.dcloud.io/component/rich-text),
- 所以使用的方法跟 vue 的父子组件传值一样,但是这里有一个坑就是不能在父组件里面直接给子组件写样式.我们可以通过拼接字符串的方式，在富文本的html中拼接一个<style></style> 标签，在拼接的 style标签内写样式，最后传给子组件。

``` bash    
    data(){
        return{
    		html:`
				<style>
                    p{
                        padding:0;
                        margin:0;
                    }
                    p{
                        font-family: PingFang-SC-Bold;
                        font-size: 4vw;
                        line-height: 7vw;		

                    }			
                    .emtitle,strong{
                        font-family: PingFang-SC-Bold;
                        font-size: 5vw;
                        font-weight: bold;
                        font-style: normal;
                    }
                    strong{display:block;margin-bottom:5vw;}
				</style>
				`,
        }
    },
    methods: {
        getHtml(){
            this.html = this.html + '<p>123456</p>'      //this.html样式 +　需要解析的富文本
        }
    }


```

## 公用部分的封装

在我的这个项目中采用了 2种封装的方式 更多封装方法 [详情](https://ask.dcloud.net.cn/article/35021) 

### 第一种 globalData

公用的方法分装在了globalData中,globalData是一种比较简单的全局变量使用方式。小程序中有个globalData概念，可以在 App 上声明全局变量。 Vue 之前是没有这类概念的，但 uni-app 引入了globalData概念，并且在包括H5、App等平台都实现了。

定义： Apa.vue

``` bash 
<script>  
	export default {
		onLaunch: function() {

		},
		onShow: function() {
			console.log('global',this.globalData.getUrl())
		},
		onHide: function() {

		},
		methods:{

		},
		globalData: {
            text: 'text'             
			getUrl() { 
				return this.text
			},

			dataFormat(item) {
				// 浏览量
				if (item.view || item.play_count) {
					let view = item.view || item.play_count
					item.viewFormat = view > 10000 ? `${Math.floor(view / 100) / 100}万` : view;
				} else {
					item.viewFormat = 0
				}
				// 日期
				if (item.time || item.times) {
					let dateStr = item.update_time || item.time
					let date
					if (typeof dateStr == "number") {
						date = new Date(dateStr * 1000)
					} else if (typeof dateStr == "string") {
						date = new Date(dateStr.replace(/-/g, "/"))
					}
					item.dateFormat = date.Format("yyyy-MM-dd")
					item.dateFormat_zh = `${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}日`;
				}
				// 时长
				if (item.duration && (typeof item.duration == "number")) {
					let m = Math.floor(item.duration / 60)
					m = m < 10 ? "0" + m : m
					let s = Math.floor(item.duration % 60)
					s = s < 10 ? "0" + s : s
					item.durationFormat = `${m}:${s}`
				}
				return item
			},
		},
	} 
</script>  

<style>  
/*每个页面公共css */  
</style>  


```
js中操作globalData的方式如下：

赋值：getApp().globalData.text = 'test'

取值：console.log(getApp().globalData.text) // 'test'
 
### 第二种 挂载 Vue.prototype  

将一些使用频率较高的常量或者方法，直接扩展到 Vue.prototype 上，每个 Vue 对象都会“继承”下来。

注意这种方式只支持多个vue页面或多个nvue页面之间公用，vue和nvue之间不公用。

示例如下：

在main.js 中挂载属性方法

``` bash
    const Host = 'https://i.*****.com/index.php?s=/document'		
    Vue.prototype.$myApi = {
        index: `${Host}/indexNav`, //首页	
        indexList: `${Host}/indexList`, //首页列表
        documentList: `${Host}/documentList`, //首页列表		
        documentInfo: `${Host}/documentInfo`, //文章详情页	
        label: `${Host}/label`, //标签列表页		
        
    }


```

## 前端 view 视图层布局注意事项

scroll-view 组件

``` bash 
<template>
    <scroll-view class="scroll-view_H" scroll-x="true" scroll-left="0">
        <block v-for="item in scrollview" :key="item.id">
            <navigator :url="'../../pages/articleContent/articleContent?id='+item.id" class="navgatorToFocus">
                <view class="scroll-view-item_H">
                    <image :src="item.pic" mode="scaleToFill"></image>
                </view>
                <view class="focusTitle"><text>{{item.title}}</text></view>
            </navigator>

        </block>
    </scroll-view>
<template>
<style>
    .scroll-view_H {
		width: 100%;
		margin-left: 35rpx;
		white-space: nowrap;     //scroll 组件必须带这个属性
	}
	.navgatorToFocus {
		display: inline-block;   // 多个循环出来的子组件 必须是 inline-block;
		height: 340rpx;
		width: 400rpx;
		margin-right: 35rpx;
		vertical-align: top;
	}    

</style>
    //

```



