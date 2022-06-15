---
title: "NodeJs面试题整理"
date: 2022-06-01T13:51:59+08:00
draft: true
categories:
  - 面试
  - NodeJs
---


###  如果 a.js require 了 b.js, 那么在 b 中定义全局变量 t = 111 能否在 a 中直接打印出来?

在执行模块代码之前，Node.js 会使用一个如下的函数包装器将其包装：

```js
(function(exports, require, module, __filename, __dirname) {
// 模块的代码实际上在这里
});
```

通过这样做，Node 实现了以下几点：

- 保持了顶层变量作用在模块范围内，而不是全局对象
- 有助于提供一些看似全局实际是特定模块的变量：
  - 导出的 `module` 和 `exports`对象
  - 包含模块绝对文件名和目录路径的便捷变量`__filename`和`__dirname`

###  a.js 和 b.js 两个文件互相 require 是否会死循环? 双方是否能导出变量? 如何从设计上避免这种问题?

### 说一下NodeJs中的错误处理？

处理错误，最常用的就是try catch 了。可是 try catch无法捕获异步错误。Node.js中，异步操作是十分常见的，异步操作主要是在回调函数中暴露错误。看一个例子：

```js
const readFile = function(path) {
 return new Promise((resolve,reject) => {
  fs.readFile(path, (err, data) => {
   if(err) { 
    throw err; // catch无法捕获错误，这和Node的eventloop有关。
        // reject(err); // catch可以捕获
      }
      resolve(data);
  });
 });
}

router.get('/xxx', async function(req, res) {
  try {
    const res = await readFile('xxx');
    ...
  } catch (e){
    // 捕获错误处理
    ...
    res.send(500);
  }
});
```

上面的代码中，readFile 中 throw 出来的错误，是无法被catch捕获的。如果我们把 throw err 换成 Promise.reject(err)，catch中是可以捕获到错误的。
**我们可以把异步操作都Promise化，然后统一使用 async 、try、catch 来处理错误。**
但是，总会有地方会被遗漏。这个时候，可以使用process来捕获全局错误，防止进程直接退出，导致后面的请求挂掉。示例代码：

```js
process.on('uncaughtException', (err) => {
  console.error(`${err.message}\n${err.stack}`);
});

process.on('unhandledRejection', (reason, p) => {
  console.error(`Unhandled Rejection at: Promise ${p} reason: `, reason);
});

```

### Eventemitter 的 emit 是同步还是异步?

**EventEmitter 会按照监听器注册的顺序同步地调用所有监听器。**

```js
const EventEmitter = require('events');
let emitter = new EventEmitter();
emitter.on('myEvent', () => {
  console.log('hi 1');
});
emitter.on('myEvent', () => {
  console.log('hi 2');
});
emitter.emit('myEvent');
// hi 1
// hi 2
```

如下函数会不会死循环？

```js
const EventEmitter = require('events');
let emitter = new EventEmitter();
emitter.on('myEvent', function sth () {
  emitter.on('myEvent', sth);
  console.log('hi');
});
emitter.emit('myEvent');
// hi
```

不会，只打印一遍hi

### 谈Node中的事件循环及其和浏览器中事件循环的区别？

[掘金：浏览器与Node的事件循环(Event Loop)有何区别?](https://juejin.cn/post/6844903761949753352)

### 以下代码，koa 会返回什么数据

```js
const Koa = require("koa");
const app = new Koa();

app.use(async (ctx, next) => {
  ctx.body = "hello, 1";
});

app.use((ctx) => {
  ctx.body = "hello, 2";
});

app.listen(3000);
// hello, 1
```

```js
const Koa = require("koa");
const app = new Koa();

app.use(async (ctx, next) => {
  ctx.body = "hello, 1";
  wait next()
});

app.use((ctx) => {
  ctx.body = "hello, 2";
});

app.listen(3000);
// hello, 2
```

```js
const Koa = require("koa");
const app = new Koa();

app.use(async (ctx, next) => {
  wait next()
  ctx.body = "hello, 1";
});

app.use((ctx) => {
  ctx.body = "hello, 2";
});

app.listen(3000);
// hello, 1
```

### Node如何进行进程间通信？
- 原生IPC通道
```js
// parent.js
const cp = require('child_process');
const n = cp.fork(`child.js`);

n.on('message', (m) => {
  console.log('父进程收到消息', m);
});

// 使子进程输出: 子进程收到消息 { hello: 'world' }
n.send({ hello: 'world' });

// child.js
process.on('message', (m) => {
  console.log('子进程收到消息', m);
});

// 使父进程输出: 父进程收到消息 { foo: 'bar', baz: null }
process.send({ foo: 'bar', baz: NaN });
```
运行：
```
> node parent.js
子进程收到消息 { hello: 'world' }
父进程收到消息 { foo: 'bar', baz: null }
```
- 通过 sockets
- 消息队列
> 参考：[Node 进程间怎么通信的](https://www.cnblogs.com/everlose/p/12846737.html)

### Node 中 require 时发生了什么？
参考：[详解Node模块加载机制]https://blog.csdn.net/ayqy_jiajie/article/details/106654770

### 简述 node/v8 中的垃圾回收机制

### node 中 exec，fork 与 spawn 有何区别？
这四个都可以用来创建子进程

1.spawn和fork都是返回一个基于流的子进程对象

2.exec和execFile可以在回调中拿到返回的buffer的内容（执行成功或失败的输出）

3.exec是创建子shell去执行命令，用来直接执行shell命令  。execFile是去创建任意你指定的文件的进程

4.fork是一种特殊的spawn，可以理解为spawn增强版，返回的子进程对象可以和父进程对象进行通信，通过send和on方法。
> 参考 
[Node官网](http://nodejs.cn/api/child_process.html#child_processspawncommand-args-options)

### node 中 dns.resolve 及 dns.lookup 有什么区别

### Node 中 require json 文件数据时，如何当文件更新时，重新 require
```js
function requireUncached(module) {
  delete require.cache[require.resolve(module)];
  return require(module);
}
```

### 在 node 中如何开启 https
```js
import path from "path";
import fs from "fs";
import express from "express";
import http from "http";
import https from "https";

const app = express();

const cred = {
  key: fs.readFileSync(path.resolve(__dirname, "../key.pem")),
  cert: fs.readFileSync(path.resolve(__dirname, "../cert.pem")),
};
const httpServer = http.createServer(app);
const httpsServer = https.createServer(cred, app);

httpServer.listen(8000);
httpsServer.listen(8888);
```

### node 中 module.exports 与 exports 有什么区别

### 在 node 中如何判断一个对象是 stream
`stream` 可以通过缓冲区来高效利用内存，从而提高性能。常用场景如读写大文件、http-server 中的大静态文件渲染。

每一个 stream 都有 `pipe` 函数，可以用来判断一个对象是否 `stream`。
代码如下，摘自 is-stream (opens new window): 一个周下载量两千万的 npm package。
```js
const isStream = (stream) =>
  stream !== null &&
  typeof stream === "object" &&
  typeof stream.pipe === "function";

isStream.writable = (stream) =>
  isStream(stream) &&
  stream.writable !== false &&
  typeof stream._write === "function" &&
  typeof stream._writableState === "object";

isStream.readable = (stream) =>
  isStream(stream) &&
  stream.readable !== false &&
  typeof stream._read === "function" &&
  typeof stream._readableState === "object";

isStream.duplex = (stream) =>
  isStream.writable(stream) && isStream.readable(stream);

isStream.transform = (stream) =>
  isStream.duplex(stream) &&
  typeof stream._transform === "function" &&
  typeof stream._transformState === "object";
```
### express 中间件的原理是什么?
一个管道，每一个中间件都是handler，经过每一步处理，最终结束。

### 简述 koa 的中间件原理，手写 koa-compose 代码

```js
function compose(middlewares) {
  return (ctx) => {
    const dispatch = (i) => {
      const middleware = middlewares[i];
      if (i === middlewares.length) {
        return;
      }
      return middleware(ctx, () => dispatch(i + 1));
    };
    return dispatch(0);
  };
}
```
### koa与express的区别？
[glory-Koa与Express的区别](https://zhuanlan.zhihu.com/p/353109449)
[进军的蜗牛-express和koa的区别 ](https://www.cnblogs.com/yalong/p/15566133.html)

### koa的中间件怎么传递消息？
参数挂到 ctx上
### 在 Node 中流 (stream) 分为几类，有哪些应用场景
Node.js 中有四种基本的流类型：

- Writable: 可以写入数据的流（例如，fs.createWriteStream()）。

- Readable: 可以从中读取数据的流（例如，fs.createReadStream()）。

- Duplex: Readable 和 Writable 的流（例如，net.Socket）。

- Transform: 可以在写入和读取数据时修改或转换数据的 Duplex 流（例如，zlib.createDeflate()）


### node和客户端怎么解决跨域的问题？

### ESModule 和 CommonJs区别是什么？

1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。

2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
