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
使用function定义一个组件时，需要使用`React.FunctionComponent`来指定组件的类型。如果要定义组件的`props`是符合哪个接口，就需要在`React.FunctionComponent`后面加`<'已定义好的interface'>`。这样定义后，在使用Icon组件时，name属性传错是就会自动报错。