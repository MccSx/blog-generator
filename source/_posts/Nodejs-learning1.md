---
title: Nodejs_learning1
date: 2020-03-10 15:50:38
tags:
  - Nodejs
categories:
  - Nodejs
---

# Node.js 初了解

## Node.js 是什么

是一个平台，它降多钟技术组合起来，让 js 也能调用系统接口、开发后端应用。
Node.js 用到了`V8引擎`、`libuv`、c/c++实现的`c-ares/http-parser/OpenSSL/zlib`等库

## Node.js 技术架构

| Node.js API（http 模块、fs 模块、stream 模块等） |                                |                    |                     |                              |
| :----------------------------------------------: | ------------------------------ | ------------------ | ------------------- | ---------------------------- |
|      Node.js bindings（让 JS 和 C/C++通信）      | C/C++插件（自定义其他能力）    |                    |                     |                              |
|                  V8（js 引擎）                   | libuv（跨平台的一部 I/O 能力） | c-ares（DNS 解析） | OpenSSL（加密解密） | http_parser、zlib（其他...） |

## API 一些重要功能

Buffer、Child Processes、Cluster、Debugger、Events、File System、Globals、HTTP、Path、Process、Query Strings、Stream、Timers、URL、Worker Threads

# Stream

## 第一个 Stream 例子

```js
const fs = require("fs");
const stream = fs.createWriteStream("./big_file.txt");
for (let i = 0; i < 1000000; i++) {
  stream.write(
    `这是第${i}行内容，我们需要很多很多内容，要不停地写文件啊啊啊啊啊\n`
  );
}
stream.end(); //别忘了关掉stream
console.log("done");
```

打开流，多次往里面塞内容，关闭流。看起来就是可以多次写，没有什么大不了的。最终我们得到一个 100M 左右的文件；
stream 是水流，但默认没有水，stream.write 可以让水流中有水（数据），每次写的小数剧叫做 chunk（块），产生数据的一段叫做 source（源头），得到数据的一段叫做 sink（水池）.

## 第二个 Stream 例子

```js
const fs = reuqire("fs");
const http = reuqire("http");
const server = http.createServer();
server.on("request", (request, response) => {
  fs.readFile("./big_file.txt", (error, data) => {
    if (error) throw error;
    response.end(data);
    console.log("done");
  });
});
server.listen(8888);
```

这个例子，会打打增加 nodejs 在内存占用量，因为这个文件有一百兆。

## 第三个例子

```js
const fs = reuqire("fs");
const http = reuqire("http");
const server = http.createServer();
server.on("request", (request, response) => {
  const stream = fs.createReadStream("./big_file.txt");
  stream.pipe(response);
  stream.on("end", () => console.log("done"));
});

server.listen(8888);
```

这个时候，nodejs 的内存占用基本不会很高。文件 stream 和 response（response 也是流） 通过管道相连。

## Stream 对象的原型链

```js
const s = fs.createReadStream(path);
```

- 自身属性：由 fs.ReadStream 构造
- 原型（一级原型）：stream.Readable.prototype
- 二级原型：stream.Stream.prototype
- 三级原型：event.EventEmitter.prototype
- 四级原型：Object.prototype

## Readable Stream

- 静止态（paused）和流动态（flowing）
  默认处于 paused，添加 data 事件监听，它会变为 flowing；删掉 data 事件监听，它就变为 paused；
  `paused()`方法可以将它变为 paused，`resume()`方法可以将它变为 flowing。

## Writable Stream

- drain 流干了的事件
  表示可以加点水了，调用`stream.write(chunk)`的时候，可能会得到 false，false 的意思是你写的太快了，数据积压了，这个时候我们 就不能再 write 了，要监听 drain 事件，等 drain 事件触发了，我们才能继续 write
- finish 事件
  调用`stream.end()`之后，而且缓冲区数据都已经给底层系统之后，会触发 finish 事件。
