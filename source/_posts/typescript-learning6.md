---
title: TypeScript+React入门ß
date: 2019-06-29 22:48:17入门
tags: 
 - JavaScript 
 - TypeScript
 - Vue
categories: TypeScript学习
---
## 项目构建
```ts
npx create-react-app my-app --typescript

// or

yarn create react-app my-app --typescript
```
安装完成后，`cd my-app`，然后`yarn start`，这样项目就正式启动了。

## 工程目录结构不同点
1. 入口文件还是index文件，不过由之前的`index.jsx`变成了`index.tsx`；
2. 所有的组件之前是`.jsx`文件，现在都是`.tsx`文件；