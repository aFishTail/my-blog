---
title: "NodeJs面试题整理"
date: 2022-06-01T13:51:59+08:00
draft: true
---


### 1、 如果 a.js require 了 b.js, 那么在 b 中定义全局变量 t = 111 能否在 a 中直接打印出来?

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

### 2、 a.js 和 b.js 两个文件互相 require 是否会死循环? 双方是否能导出变量? 如何从设计上避免这种问题?

### 3、说一下NodeJs中的错误处理？

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

### 4、Eventemitter 的 emit 是同步还是异步?

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

### 5、谈Node中的事件循环及其和浏览器中事件循环的区别？

### 6、以下代码，koa 会返回什么数据
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

### 7、Node如何进行进程间通信？

### 8、Node 中 require 时发生了什么？

### 9、简述 node/v8 中的垃圾回收机制

### 10、node 中 exec，fork 与 spawn 有何区别？

### 11、node 中 dns.resolve 及 dns.lookup 有什么区别

### 12、Node 中 require json 文件数据时，如何当文件更新时，重新 require

### 13、在 node 中如何开启 https

### 14、node 中 module.exports 与 exports 有什么区别

### 15、在 node 中如何判断一个对象是 stream

### 16、简述 koa 的中间件原理，手写 koa-compose 代码
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
### 17、在 Node 中流 (stream) 分为几类，有哪些应用场景

### 18、node和客户端怎么解决跨域的问题？





