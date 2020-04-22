---
title: Express
date: 2020-03-31 15:23:23
tags:
  - Nodejs Expressjs
categories:
  - Nodejs Expressjs
---

# Express 入门

## curl -v

- curl -s -v 网址
  `-s`是 client，用于隐藏进度条；`-v`是 verbose，用于打印全部 header；`*`开头是注释；`>`开头是 http 请求；`<`开头是 http 相应。
- 举例
  `curl -s -v 网址`，此时有时会得到 301 和 Location，于是需要重新请求，在命令里多加一个`-L`就表示自动重定向。
  `curl -o nul -s -v 网址`，`-o nul`是为了隐藏 html 文本，内容过于不方便查看信息。Linux 和 Macos 需要将`nul`改成`/dev/null`

## 请求和响应

- 请求
  如果请求体内容为 JSON，那么请求头就要有`Content-Type: application/json`，具体其他格式可查看 MDN。
- 响应
  如果响应体的内容为 JSON，那么响应头就要有`Content-Type: application/json`。

## Web 框架

- 功能 1.更方便地处理 HTTP 请求与响应； 2.更方便地链接数据库、Redies； 3.更方便的路由； 4.其他：HTML 模板
- 理念
  Web 框架的主流思路都是 MVC，Model 处理数据相关逻辑，View 处理视图相关逻辑，前后分离之后，View 就不重要了，Controller 负责其他逻辑。

## Express 编程模型
