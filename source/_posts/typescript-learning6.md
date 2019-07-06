---
title: TypeScript+React入门
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

<!-- more -->

## 简单写一个按钮组件
```ts
// Button.tsx
import React from 'react';
import './Button.css'

class Button extends React.Component {
    render() {
        return (
            <div className="btn">{this.props.children}</div>
        );
    }
}

export default Button;
```
```css
 /* Button.css */
.btn{
    display: inline-block;
    border: 1px solid black;
    border-radius: 4px;
    padding: 4px;
    cursor: pointer;
}
```
```ts
// App.tsx
import React from 'react';
import Button from './Button';

const App: React.FC = () => {
  return (
    <div className="App">
      <Button>Click</Button>
    </div>
  );
}

export default App;
```

#### 接收props
如果此时在`Button`组件上传一个值：`<Button size="big">Click</Button>`，此时还未编译就会报错，报错提示：**不能将类型“{ children: string; size: string; }”分配给类型“IntrinsicAttributes...**，其实意思就是`Button`组件没有定义接收`size`属性。
为了解决这个报错，那就要在`Button`组件内定义`size`属性，有下面几种方式：
1. `class Button extends React.Component<{size: string}>...`，直接在`React.Component`后面加一个泛型，只是这种方式在有多个属性时就会写得很乱，不推荐；
2. `type Props = {size: string;}` `class Button extends React.Component<Props>`，用`type`关键字声明一个类型别名；
3. `interface Props = {size: string;}` `class Button extends React.Component<Props>`，直接定义一个接口，普遍用这种方式；

#### constructor构造函数传参
```ts
import React from 'react';
import './Button.css'

interface IProps {
    size: string;
}

class Button extends React.Component<IProps> {
    constructor(props) {  //报错，参数“props”隐式具有“any”类型。
        super(props)
    }
    render() {
        return (
            <div className="btn">{this.props.children}</div>
        );
    }
}

export default Button;
```
上述代码，`constructor`构造器中直接传入`props`参数会报错， 必须定义`props`参数是上述定义的`IProps`接口类型`constructor(props: IProps)`。

#### 定义state内部属性类型
```ts
import React from 'react';
import './Button.css'

interface IProps {
    size: string;
}

class Button extends React.Component<IProps> {
    constructor(props: IProps) {
        super(props);
        this.state = {
            n: 1
        }
    }
    render() {
        return (
            <div className="btn">{this.props.children}{this.state.n}</div>
        );  // 报错，类型“Readonly<{}>”上不存在属性“n”。
    }
}

export default Button;
```
上述代码这样直接写`state`，在下面代码使用时会报错，根据报错信息，其实意思就是没有定义`n`是什么类型，所以需要事先定义好`n`的类型，增加如下代码：
```ts
interface IState {
    n: number;
}
class Button extends React.Component<IProps, IState> {
    constructor(props: IProps) {
        super(props);
        this.state = {
            n: 1
        }
    }
    render() {
        return (
            <div className="btn">{this.props.children}{this.state.n}</div>
        );
    }
}
```

#### 使用函数回调
```ts
// App.tsx
class App extends React.Component {
  click(e: React.MouseEvent) {
    console.log('click')
    console.log(e)
  }
  render() {
    return (
      <div className="App">
        <Button size="big" onClick={this.click}>Click</Button>
      </div>
    );
  }
}
```
```ts
// Button.tsx
interface IProps {
    size: string;
    onClick: React.MouseEventHandler;
}
interface IState {
    n: number;
}

class Button extends React.Component<IProps, IState> {
    constructor(props: IProps) {
        super(props);
        this.state = {
            n: 1
        }
    }
    render() {
        return (
            <div className="btn" onClick={this.props.onClick}>{this.props.children}{this.state.n}</div>
        );
    }
}
```
如上述代码，`Button`组件中需要定义函数类型，由于是一个鼠标事件，定义为`React.MouseEventHandler`；在App文件中，在函数中使用`e`这个参数，也需要定义类型为`React.MouseEvent`。