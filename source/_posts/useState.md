---
title: React useState
date: 2019-07-14 23:43:00
tags:
 - React
categories:
 - React
---
## useState基础用法
```js
function App() {
    const [n, setN] = React.useState(0)
    return (
        <div className="app">
            <p>{n}</p>
            <button onClick={() => setN(n + 1)}>n+1</button>
        </div>
    )
}

ReactDOM.render(<App />, rootElement)
```
 - 首次渲染：`render <App />`，调用App函数，得到虚拟div（一个对象），创建真实div；
 - 当点击button时，会调用setN函数，再次 `render <App />`；
 - 再次调用App函数，得到虚拟div，DOM diff更新真实div；
每次运行App函数，都会运行`useState(0)`，由此看似运行`useState(0)`，变量n又是0，但其实不然，第二次调用时n变为了1。
## 原理分析
 - `setN`一定会修改某个数据x，把`n+1`的值存入x；
 - `setN`一定会出发`App()`，重新渲染；
 - `useState`肯定会从数据x里读取n的最新值；
 - 那每个组件应该会有自己的一个数据x，暂时将其命名为`state`
`useState`原理类似如下代码：
```js
let _state;

function myUseState(initialValue) {
  _state = _state || initialValue;
  function setState(newState) {
    _state = newState;
    render();
  }
  return [_state, setState];
}

const render = () => ReactDOM.render(<App />, rootElement);

function App() {
  const [n, setN] = myUseState(0);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
      </p>
    </div>
  );
}

ReactDOM.render(<App />, rootElement);
```
这样的原理，其实还是有问题的，因为如果App函数内不止一次调用了`useState`函数，那这个state就会冲突。
## 进一步源码改进
```js
let _state = [];
let index = 0;

function myUseState(initialValue) {
  const currentIndex = index;
  index += 1;
  _state[currentIndex] = _state[currentIndex] || initialValue;
  const setState = newState => {
    _state[currentIndex] = newState;
    render();
  };
  return [_state[currentIndex], setState];
}

const render = () => {
  index = 0;
  ReactDOM.render(<App />, rootElement);
};

function App() {
  const [n, setN] = myUseState(0);
  const [m, setM] = myUseState(0);
  console.log(_state);
  return (
    <div className="App">
      <p>{n}</p>
      <p>
        <button onClick={() => setN(n + 1)}>+1</button>
      </p>
      <p>{m}</p>
      <p>
        <button onClick={() => setM(m + 1)}>+1</button>
      </p>
    </div>
  );
}

ReactDOM.render(<App />, rootElement);
```
用次方案会有一个缺陷，就是调用`useState`一定要注意顺序，所以再调用时，一定不能放在`if`里边。而实际调用`useState`时如果放在了`if`内，React会报错。
## 总结
 - 每个函数组件对应一个`React`节点
 - 每个节点保存着`state`和`index`
 - `useState`会读取`state[index]`
 - `index`由`useState`出现的顺序决定
 - `setState`会修改`state`，并处罚更新