---
title: react造轮子-Icon组件
date: 2019-07-14 23:43:00
tags: 
 - TypeScript
 - React
categories: 
 - TypeScript学习
 - React
---
## 使用React.FunctionComponent与接收props
```ts
import React from 'react';

interface IconProps {
    name: string;
}

const Icon: React.FunctionComponent<IconProps> = (props) => {
    return <span>{props.name}</span>
}

export default Icon;
```
使用function定义一个组件时，需要使用`React.FunctionComponent`来指定组件的类型。