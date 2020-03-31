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

# child_process

## 进程 Process

- 场景
  notepad.exe 是一个程序，不是进程，双击 notepad.exe 时，操作系统会开启一个进程。
- 定义
  **进程**是**程序**的执行实例，**程序**在 CPU 上执行是的活动叫做进程，实际上并没有明确的定义，只有一些规则。
- 特点
  一个进程可以创建另一个进程（父进程与子进程），通过任务管理器可以看到进程。

## 了解 CPU

- 特点
  一个单核 CPU，在一个时刻，只能做一件事情，那么如何让用户同时看电影、听音乐、写代码呢？答案是在不同的进程中快速切换。
- 多程序并发执行
  值多个程序在宏观上并行，围观上串行，每个进程会出现「执行-暂停-执行」的规律，多个进程之前会出现抢资源的现象。

## 阻塞

等待执行的过程中，都是非运行态。一些（A）在等待 CPU 资源，另一些（B）在等待 I/O 完成（如文件读取），如果这个时候吧 CPU 分配给 B 进程，B 还是在等 I/O，我们吧这个 B 叫做阻塞进程。因此，分派程序只会吧 CPU 分配给非阻塞进程。

## 线程 Thread 的引入

- 分阶段
  在面向进程设计的系统中，进程是程序的基本执行实体。在面向线程设计的系统中，进程本身不是基本运行单位，而是线程的容器。
- 引入原因
  进程是执行的基本实体，也是资源分配的基本实体，导致进程的创建、切换、销毁太消耗 CPU 时间了，于是引入线程，线程作为执行的基本实体，而进程只作为资源分配的基本实体。

## 线程 Thread

- 概念
  CPU 调度和执行的最小单位。一个进程中至少有一个线程，可以有多个线程。一个进程中的线程共享改进程的所有资源。进程的第一个线程叫做初始化线程。线程的调度可以由操作系统负责，也可以用户自己负责。
- 举例
  浏览器进程里面有渲染引擎、V8 引擎、存储模块、网络模块、用户界面模块等。每个模块都可以放在一个线程里。

## Nodejs 里边的 child_process

- 使用目的
  子进程的运行结果存储在系统缓存之中（最大 200kb），等到子进程运行结束以后，主进程再用回调函数读取子进程的运行结果。

## child_process API

- exec(cmd, options, fn)
  execute 的缩写，用于执行 bash 命令，同步版本：`execSync`

```js
const { exec } = require("child_process");
exec("ls ../", (error, stdout) => {
  console.log(error);
  console.log(stdout);
});
```
