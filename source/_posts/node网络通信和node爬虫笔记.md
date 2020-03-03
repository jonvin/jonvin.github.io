---
title: node网络通信和node爬虫笔记
date: 2019-11-23 16:28:41
tags:
---
前言：在这一章节里，我写了两大模块的笔记，因为这个两个模块又同属于属于node.js 的范畴，因此把它们写在了一起。


## node 网络通信 

Node 是一个面向网络而生的平台，它具有事件驱动、无阻塞、单线程等特性，具备良好的可伸缩性，使得它十分轻量，适合在分布式网络中扮演各种各样的角色。同时 Node 提供的 API 十分贴合网络，适合用它基础的 API 构建灵活的网络服务。

利用 Node 可以十分方便的搭建网络服务器。在 Web 领域，大多数的编程语言需要专门的 Web 服务器作为容器，如 ASP、ASP.NET 需要 IIS 作为服务器，PHP 需要打在 Apache 或 Nginx 环境等，JSP 需要 Tomcat 服务器等。但对于 Node 而言，只需要几行代码即可构建服务器，无需额外的容器。


Node 提供了 net、dgram、http、https 这4个模块，分别用于处理 TCP、UDP、HTTP、HTTPS，适用于服务器端和客户端。


### TCP 和 UDP

> **TCP和UDP: 都是数据传输方式的协议.比如说我要给你钱, 我是以手把手的方式拿给你呢还是以快递的方式寄给你呢.**

TCP（传输控制协议）

- 需要建立连接（三次握手）,形成一条传输通道,才能传输数据
- 传输数据的大小不受限制
- 是安全可靠的协议,但是速度稍慢

UDP（用户数据报协议）

概念：英文全拼（User Datagram Protocol）简称用户数据报协议，它是无连接的、不可靠的网络传输协议

**核心特点**：无连接、资源开销小、传输速度快、UDP每个数据包最大是64K

**优点**：不需要连接，传输速度快，资源开销小

**缺点**：传输数据不可靠，容易丢失数据包，没有流量控制，当对方没有及时接收数据，发送方一直发送数据会导致缓冲区数据满了，电脑出现卡死情况，所以接收方需要及时接收数据

- 不需要建立连接, 把数据封装成数据包扔给对面
- 每个数据包大小限制在64K内
- 因为不建立连接,所以对方可能收到也可能收不到数据(丢包),因此是不安全的协议, 但是速度比较快

什么时候用 TCP，什么时候用 UDP？

- 对速度要求比较高的时候使用UDP，例如视频聊天, QQ聊天
- 对数据安全要求比较高的时候使用TCP，例如数据传输,文件下载
- 假如对于视频聊天来说,如果画质优先那就选用TCP, 如果流畅度优先那就选用UDP


##### TCP基本示例

- Node 中的核心模块 net

服务端： 

```javascript
	const net = require('net');
	const server = net.createServer((c) => {
	  // 'connection' listener
	  console.log('client connected');
	  c.on('end', () => {
	    console.log('client disconnected');
	  });
	  c.write('hello\r\n');
	  c.pipe(c);
	});
	server.on('error', (err) => {
	  throw err;
	});
	server.listen(8124, () => {
	  console.log('server bound');
	});
```

客户端： 

```javascript
	const net = require('net');
	const client = net.createConnection({ port: 8124 }, () => {
	  // 'connect' listener
	  console.log('connected to server!');
	  client.write('world!\r\n');
	});
	client.on('data', (data) => {
	  console.log(data.toString());
	  client.end();
	});
	client.on('end', () => {
	  console.log('disconnected from server');
	});
```


##### 服务端相关 API

> [Class: net.Server](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_class_net_server)

- [new net.Server([options\][, connectionListener])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_new_net_server_options_connectionlistener) 创建服务器
- [Event: 'close'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_close) 当服务器关闭时，触发该事件
- [Event: 'connection'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_connection) 当有新的客户端连接进来时，触发该事件
- [Event: 'error'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_error) 当服务器发生错误时，触发该事件
- [Event: 'listening'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_listening) 当调用 server.listen() 绑定端口后触发，简洁写法为 server.listen(port, listeningListener)，通过 listen() 方法的第二个参数传入
- [server.address()](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_address) 服务器创建侦听成功后，可以用来获取服务器地址相关信息，包含 `{ port: 12346, family: 'IPv4', address: '127.0.0.1' }` 信息
- [server.close([callback])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_close_callback) 当服务器关闭时触发，在调用 server.close() 后，服务器将停止接受新的套接字连接，但保持当前存在的连接，等待所有连接都断开后，会触发该事件
- [server.connections](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_connections) 获取当前已建立连接的数量
  - 注意：该 API 即将废弃，推荐使用 `server.getConnections()` 替换
- [server.getConnections(callback)](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_getconnections_callback) 获取当前已建立连接的数量
- [server.listen()](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_listen) 绑定端口号启动服务，开始等待侦听
  - [server.listen(handle[, backlog\][, callback])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_listen_handle_backlog_callback)
  - [server.listen(options[, callback\])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_listen_options_callback)
  - [server.listen(path[, backlog\][, callback])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_listen_path_backlog_callback)
  - [server.listen([port[, host[, backlog\]]][, callback])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_listen_port_host_backlog_callback)
- [server.listening](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_listening) 获取服务器的侦听状态
- [server.maxConnections](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_maxconnections) 在服务器连接数较高的时候，可以通过设置该属性用于拒绝超过最大数的连接
- [server.ref()](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_ref) 恢复服务器侦听
- [server.unref()](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_server_unref) 暂停服务器侦听，可以使用 `server.ref()` 恢复服务器侦听



### 套接字 Socket 相关 API

> [Class: net.Socket](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_class_net_socket)

- [new net.Socket([options])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_new_net_socket_options) 创建 Socket 连接
- [Event: 'close'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_close_1) 当套接字完全关闭时，触发该事件
- [Event: 'connect'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_connect) 该事件用于客户端，当套接字与服务端连接成功时会被触发
- [Event: 'data'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_data) 当一端调用 `write()` 发送数据时，另一端会触发 `data` 事件，事件传递的数据即是 `write()` 发送的数据
- [Event: 'drain'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_drain) 当任意一端调用 write() 发送数据时，当前这端会触发该事件
- [Event: 'end'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_end) 当连接中的任意一端发送了 FIN 数据时，将会触发该事件
- [Event: 'error'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_error_1) 当异常发生时，触发该事件
- [Event: 'timeout'](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_event_timeout)  当一定时间后连接不再活跃时，该事件将会被触发，通知用户当前连接已经被闲置了
- [socket.address()](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_address) 获取套接字的连接信息，例如 `{ port: 12346, family: 'IPv4', address: '127.0.0.1' }`
- [socket.localAddress](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_localaddress) 获取当前套接字地址
- [socket.localPort](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_localport) 获取当前套接字端口号
- [socket.remoteAddress](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_remoteaddress) 获取另一端套接字地址
- [socket.remoteFamily](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_remotefamily) 获取另一端套接字IP协议版本
- [socket.remotePort](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_remoteport) 获取另一端套接字连接端口号
- [socket.setEncoding([encoding])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_setencoding_encoding) 设置获取数据解析的编码格式，默认不处理
- [socket.write(data[, encoding][, callback])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_socket_write_data_encoding_callback) 通过套接字发送数据



##### 其它 API

- [net.connect()](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_connect) 创建 Socket 客户端连接，和 `net.createConnection()` 作用相等
  - [net.connect(options[, connectListener])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_connect_options_connectlistener)
  - [net.connect(port[, host\][, connectListener])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_connect_port_host_connectlistener)
- [net.createConnection()](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_createconnection) 创建 Socket 客户端连接
  - [net.createConnection(options[, connectListener])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_createconnection_options_connectlistener)
  - [net.createConnection(path[, connectListener])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_createconnection_path_connectlistener)
  - [net.createConnection(port[, host\][, connectListener])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_createconnection_port_host_connectlistener)
- [net.createServer([options\][, connectionListener])](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_createserver_options_connectionlistener) 创建Socket服务端，等价于 `new net.Server()`
- [net.isIP(input)](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_isip_input) 判断是否是IP地址
- [net.isIPv4(input)](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_isipv4_input) 判断是否是符合 IPv4协议的地址
- [net.isIPv6(input)](https://nodejs.org/dist/latest-v10.x/docs/api/net.html#net_net_isipv6_input) 判断是否是符合 IPv6 协议的地址


##### UDP基本示例

- Node 中的核心模块 dgram
- 使用 Node 实现 UDP 单播

服务端： 

```javascript
	const dgram = require('dgram')

	const server = dgram.createSocket('udp4')

	server.on('listening', () => {
	  const address = server.address()
	  console.log(`server running ${address.address}:${address.port}`)
	})

	server.on('message', (msg, remoteInfo) => {
	  console.log(`server got ${msg} from ${remoteInfo.address}:${remoteInfo.port}`)
	  server.send('world', remoteInfo.port, remoteInfo.address)
	})

	server.on('error', err => {
	  console.log('server error', err)
	})

	server.bind(3000)

```


客户端:

```javascript
	const dgram = require('dgram')

	const client = dgram.createSocket('udp4')

	// client.send('hello', 3000, 'localhost')

	client.on('listening', () => {
	  const address = client.address()
	  console.log(`client running ${address.address}:${address.port}`)

	  client.send('hello', 3000, 'localhost')
	})

	client.on('message', (msg, remoteInfo) => {
	  console.log(`client got ${msg} from ${remoteInfo.address}:${remoteInfo.port}`)
	})

	client.on('error', err => {
	  console.log('client error', err)
	})

	client.bind(8000)	

```

### node 中的 http 模块

TCP 和 UDP 都属于网络传输层协议，如果要构建高效的网络应用，就应该从传输层进行着手。但是对于经典的浏览器网页和服务端通信场景，如果单纯的使用更底层的传输层协议则会变得麻烦。所以对于经典的B（Browser）S（Server）通信，基于传输层之上专门制定了更上一层的通信协议：HTTP，用于浏览器和服务端进行通信。由于 HTTP 协议本身并不考虑数据如何传输及其他细节问题，所以属于应用层协议。

Node 提供了基本的 http 和 https 模块用于 HTTP 和 HTTPS 的封装。

##### Server 实例

| API              | 说明               |
| ---------------- | ------------------ |
| Event：'close'   | 服务关闭时触发     |
| Event：'request' | 收到请求消息时触发 |
| server.close()   | 关闭服务           |
| server.listening | 获取服务状态       |



##### 请求对象

| API                 | 说明             |
| ------------------- | ---------------- |
| request.method      | 请求方法         |
| request.url         | 请求路径         |
| request.headers     | 请求头           |
| request.httpVersion | 请求HTTP协议版本 |



##### 响应对象

| API                                | 说明             |
| ---------------------------------- | ---------------- |
| response.end()                     | 结束响应         |
| response.setHeader(name, value)    | 设置响应头       |
| response.removeHeader(name, value) | 删除响应头       |
| response.statusCode                | 设置响应状态码   |
| response.statusMessage             | 设置响应状态短语 |
| response.write()                   | 写入响应数据     |
| response.writeHead()               | 写入响应头       |



## 网络爬虫开发

爬虫就是一个探测程序，它的基本功能就是模拟人的行为去各个网站转悠，点点按钮，找找数据，或者把看到的信息背回来。就像一只虫子在一幢楼里不知疲倦地爬来爬去。

### 将获取的HTML字符串使用cheerio 爬虫工具来解析

##### cheerio库简介

> 这是一个核心api按照jquery来设计，专门在服务器上使用，一个微小、快速和优雅的实现

简而言之，就是可以再服务器上用这个库来解析HTML代码，并且可以直接使用和jQuery一样的api

官方demo如下：

```js
const cheerio = require('cheerio')
const $ = cheerio.load('<h2 class="title">Hello world</h2>')
 
$('h2.title').text('Hello there!')
$('h2').addClass('welcome')
 
$.html()
//=> <html><head></head><body><h2 class="title welcome">Hello there!</h2></body></html>
```



2. 使用jQuery API获取所有img的src属性

```js
const http = require('http')
const cheerio = require('cheerio')
let req = http.request('http://XXX.com/teacher.html', res => {
  let chunks = []
  res.on('data', chunk => {
    chunks.push(chunk)
  })
  res.on('end', () => {
    // console.log(Buffer.concat(chunks).toString('utf-8'))
    let html = Buffer.concat(chunks).toString('utf-8')
    let $ = cheerio.load(html)
    let imgArr = Array.prototype.map.call($('.tea_main .tea_con .li_img > img'), (item) => 'http://web.XXXX.com/' + $(item).attr('src'))
    console.log(imgArr)
    // let imgArr = []
    // $('.tea_main .tea_con .li_img > img').each((i, item) => {
    //   let imgPath = 'http://web.XXXX.com/' + $(item).attr('src')
    //   imgArr.push(imgPath)
    // })
    // console.log(imgArr)
  })
})

req.end()
```


##### 使用download库批量下载图片

```js
const http = require('http')
const cheerio = require('cheerio')
const download = require('download')
let req = http.request('http://web.XXX.com/teacher.html', res => {
  let chunks = []
  res.on('data', chunk => {
    chunks.push(chunk)
  })
  res.on('end', () => {
    // console.log(Buffer.concat(chunks).toString('utf-8'))
    let html = Buffer.concat(chunks).toString('utf-8')
    let $ = cheerio.load(html)
    let imgArr = Array.prototype.map.call($('.tea_main .tea_con .li_img > img'), (item) => encodeURI('http://web.XXXX.com/' + $(item).attr('src')))
    // console.log(imgArr)

    Promise.all(imgArr.map(x => download(x, 'dist'))).then(() => {
      console.log('files downloaded!');
    });
  })
})

req.end()
```


##### 爬取动态网站

大部分新闻网站，现在都采取前后端分离的方式，也就是前端页面先写好模板，等网页加载完毕后，发送Ajax再获取数据，将其渲染到模板中。所以如果使用相同方式来获取目标网站的HTML页面，请求到的只是模板，并不会有数据，此时，如果还希望使用当前方法爬取数据，就需要分析该网站的ajax请求是如何发送的，可以打开network面板来调试：

分析得出对应的ajax请求后，找到其URL，向其发送请求即可

代码如下：

```js
// 引入http模块
const http = require('http')

// 创建请求对象 (此时未发送http请求)
const url = 'http://www.XXX.cn/news/json/f1f5ccee-1158-49a6-b7c4-f0bf40d5161a.json'
let req = http.request(url, res => {
  // 异步的响应
  // console.log(res)
  let chunks = []
  // 监听data事件,获取传递过来的数据片段
  // 拼接数据片段
  res.on('data', c => chunks.push(c))

  // 监听end事件,获取数据完毕时触发
  res.on('end', () => {
    // 拼接所有的chunk,并转换成字符串 ==> html字符串
    // console.log(Buffer.concat(chunks).toString('utf-8'))
    let result = Buffer.concat(chunks).toString('utf-8')
    console.log(JSON.parse(result))
  })
})

// 将请求发出去
req.end()
```

如果遇到请求限制，还可以模拟真实浏览器的请求头：

```js
// 引入http模块
const http = require('http')
const cheerio = require('cheerio')
const download = require('download')

// 创建请求对象 (此时未发送http请求)
const url = 'http://www.XXXX.cn/news/json/f1f5ccee.json'
let req = http.request(url, {
  headers: {
    "Host": "www.XXXX.cn",
    "Connection": "keep-alive",
    "Content-Length": "0",
    "Accept": "*/*",
    "Origin": "http://www.XXXX.cn",
    "X-Requested-With": "XMLHttpRequest",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36",
    "DNT": "1",
    "Referer": "http://www.XXXX.cn/newsvideo/newslist.html",
    "Accept-Encoding": "gzip, deflate",
    "Accept-Language": "zh-CN,zh;q=0.9,en;q=0.8",
    "Cookie": "UM_distinctid=16b8a0c1ea534c-0c311b256ffee7-e343166-240000-16b8a0c1ea689c; bad_idb2f10070-624e-11e8-917f-9fb8db4dc43c=8e1dcca1-9692-11e9-97fb-e5908bcaecf8; parent_qimo_sid_b2f10070-624e-11e8-917f-9fb8db4dc43c=921b3900-9692-11e9-9a47-855e632e21e7; CNZZDATA1277769855=1043056636-1562825067-null%7C1562825067; XXXX.cn=TUd3emFUWjBNV2syWVRCdU5XTTRhREZs; PHPSESSID=j3ppafq1dgh2jfg6roc8eeljg2; CNZZDATA4617777=cnzz_eid%3D926291424-1561388898-http%253A%252F%252Fmail.XXXX.cn%252F%26ntime%3D1563262791; Hm_lvt_0cb375a2e834821b74efffa6c71ee607=1561389179,1563266246; qimo_seosource_22bdcd10-6250-11e8-917f-9fb8db4dc43c=%E7%AB%99%E5%86%85; qimo_seokeywords_22bdcd10-6250-11e8-917f-9fb8db4dc43c=; href=http%3A%2F%2Fwww.XXX.cn%2F; bad_id22bdcd10-6250-11e8-917f-9fb8db4dc43c=f2f41b71-a7a4-11e9-93cc-9b702389a8cb; nice_id22bdcd10-6250-11e8-917f-9fb8db4dc43c=f2f41b72-a7a4-11e9-93cc-9b702389a8cb; openChat22bdcd10-6250-11e8-917f-9fb8db4dc43c=true; parent_qimo_sid_22bdcd10-6250-11e8-917f-9fb8db4dc43c=fc61e520-a7a4-11e9-94a8-01dabdc2ed41; qimo_seosource_b2f10070-624e-11e8-917f-9fb8db4dc43c=%E7%AB%99%E5%86%85; qimo_seokeywords_b2f10070-624e-11e8-917f-9fb8db4dc43c=; accessId=b2f10070-624e-11e8-917f-9fb8db4dc43c; pageViewNum=2; nice_idb2f10070-624e-11e8-917f-9fb8db4dc43c=20d2a1d1-a7a8-11e9-bc20-e71d1b8e4bb6; openChatb2f10070-624e-11e8-917f-9fb8db4dc43c=true; Hm_lpvt_0cb375a2e834821b74efffa6c71ee607=1563267937"
  }
}, res => {
  // 异步的响应
  // console.log(res)
  let chunks = []
  // 监听data事件,获取传递过来的数据片段
  // 拼接数据片段
  res.on('data', c => chunks.push(c))

  // 监听end事件,获取数据完毕时触发
  res.on('end', () => {
    // 拼接所有的chunk,并转换成字符串 ==> html字符串
    // console.log(Buffer.concat(chunks).toString('utf-8'))
    let result = Buffer.concat(chunks).toString('utf-8')
    console.log(JSON.parse(result))
  })
})

// 将请求发出去
req.end()
```


### 使用Selenium实现爬虫

##### Selenium API学习

核心对象：

- Builder
- WebDriver
- WebElement

辅助对象：

- By
- Key


##### WebDriver

通过构造器创建好WebDriver后就可以使用API查找网页元素和获取信息了：

- findElement()   查找元素


##### WebElement

- getText()  获取文本内容
- sendKeys()  发送一些按键指令
- click()  点击该元素


##### 自动打开拉勾网搜索"前端"

1. 使用driver打开拉勾网主页

2. 找到全国站并点击一下
3. 输入“前端”并回车

```js
const { Builder, By, Key } = require('selenium-webdriver');

(async function start() {
  let driver = await new Builder().forBrowser('chrome').build();
  await driver.get('https://www.lagou.com/');
  await driver.findElement(By.css('#changeCityBox .checkTips .tab.focus')).click();
  await driver.findElement(By.id('search_input')).sendKeys('前端', Key.ENTER);
})();
```

##### 获取需要的数据

使用driver.findElement()找到所有条目项，根据需求分析页面元素，获取其文本内容即可：

```js
const { Builder, By, Key } = require('selenium-webdriver');

(async function start() {
  let driver = await new Builder().forBrowser('chrome').build();
  await driver.get('https://www.lagou.com/');
  await driver.findElement(By.css('#changeCityBox .checkTips .tab.focus')).click();
  await driver.findElement(By.id('search_input')).sendKeys('前端', Key.ENTER);
  let items = await driver.findElements(By.className('con_list_item'))
  items.forEach(async item => {
    // 获取岗位名称
    let title = await item.findElement(By.css('.p_top h3')).getText()
    // 获取工作地点
    let position = await item.findElement(By.css('.p_top em')).getText()
    // 获取发布时间
    let time = await item.findElement(By.css('.p_top .format-time')).getText()
    // 获取公司名称
    let companyName = await item.findElement(By.css('.company .company_name')).getText()
    // 获取公司所在行业
    let industry = await item.findElement(By.css('.company .industry')).getText()
    // 获取薪资待遇
    let money = await item.findElement(By.css('.p_bot .money')).getText()
    // 获取需求背景
    let background = await item.findElement(By.css('.p_bot .li_b_l')).getText()
    // 处理需求背景
    background = background.replace(money, '')
    console.log(title, position, time, companyName, industry, money, background)
  })
})();
```


##### 自动翻页

思路如下：

1. 定义初始页码
2. 获取数据后，获取页面上的总页码，定义最大页码
3. 开始获取数据时打印当前正在获取的页码数
4. 获取完一页数据后，当前页码自增，然后判断是否达到最大页码
5. 查找下一页按钮并调用点击api，进行自动翻页
6. 翻页后递归调用获取数据的函数

```js
const { Builder, By, Key } = require('selenium-webdriver');

let currentPageNum = 1;
let maxPageNum = 1;
let driver = new Builder().forBrowser('chrome').build();

(async function start() {
  await driver.get('https://www.lagou.com/');
  await driver.findElement(By.css('#changeCityBox .checkTips .tab.focus')).click();
  await driver.findElement(By.id('search_input')).sendKeys('前端', Key.ENTER);
  maxPageNum = await driver.findElement(By.className('totalNum')).getText()
  getData()
})();

async function getData() {
  console.log(`正在获取第${currentPageNum}页的数据, 共${maxPageNum}页`)
  while (true) {
    let flag = true
    try {
      let items = await driver.findElements(By.className('con_list_item'))
      let results = []
      for (let i = 0; i < items.length; i++) {
        let item = items[i]
        // 获取岗位名称
        let title = await item.findElement(By.css('.p_top h3')).getText()
        // 获取工作地点
        let position = await item.findElement(By.css('.p_top em')).getText()
        // 获取发布时间
        let time = await item.findElement(By.css('.p_top .format-time')).getText()
        // 获取公司名称
        let companyName = await item.findElement(By.css('.company .company_name')).getText()
        // 获取公司所在行业
        let industry = await item.findElement(By.css('.company .industry')).getText()
        // 获取薪资待遇
        let money = await item.findElement(By.css('.p_bot .money')).getText()
        // 获取需求背景
        let background = await item.findElement(By.css('.p_bot .li_b_l')).getText()
        // 处理需求背景
        background = background.replace(money, '')
        // console.log(id, job, area, money, link, need, companyLink, industry, tags, welfare)
        results.push({
          title,
          position,
          time,
          companyName,
          industry,
          money,
          background
        })
      }

      console.log(results)

      currentPageNum++
      if (currentPageNum <= maxPageNum) {
        await driver.findElement(By.className('pager_next')).click()
        await getData(driver)
      }
    } catch (e) {
      // console.log(e.message)
      if (e) flag = false
    } finally {
      if (flag) break
    }
  }
}
```