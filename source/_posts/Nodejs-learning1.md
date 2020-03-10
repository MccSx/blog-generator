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
