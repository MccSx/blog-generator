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

## 使用 React.FunctionComponent 与接收 props

```ts
import React from "react";

interface IconProps {
  name: string;
}

const Icon: React.FunctionComponent<IconProps> = props => {
  return <span>{props.name}</span>;
};

export default Icon;
```

使用 function 定义一个组件时，需要使用`React.FunctionComponent`来指定组件的类型。如果要定义组件的`props`是符合哪个接口，就需要在`React.FunctionComponent`后面加`<'已定义好的interface'>`。这样定义后，在使用 Icon 组件时，name 属性传错是就会自动报错。

## 引入 svg 文件

```js
import wechat from "./icon/wechat.svg";
```

首先引入 svg 文件，这时候会报错，需要安装相应的 loader，并且 webpack 和 tsconfig 都需要做配置。

- 安装`svg-sprite-loader`，并且在`webpack.config.js`文件做出相应 loader 配置
- 在 types 目录下的`custom.d.ts`文件中添加配置声明

```js
declare module '*.svg' {
    const content: any;
    export default content;
}
```

- 在`tsconfig.json`文件内添加如下规则

```js
"include": [
    "lib/**/*",
    "types/**/*"
],
```

## 在标签内使用 svg 文件

```js
import "./icon/wechat.svg";
import React from "react";

interface IconProps {
  name: string;
}

const Icon: React.FunctionComponent<IconProps> = props => {
  return (
      <span>
          <svg>
              <use xlinkHref="#wechat"}</use>
          </svg>
      </span>
  );
};

export default Icon;
```

## 让 Icon 组件响应所有事件

```js
interface IconProps extends React.SVGAttributes<SVGElement> {
  name: string;
}
```

`IconProps`这个接口，不可能把所有事件都定义一遍，因为事件太多了，只需`IconProps`接口继承与`React.SVGAttributes<SVGElement>`就可以。
