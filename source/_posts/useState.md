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
每次运行App函数，都会运行`useState(0)`，由此看似运行`useState(0)`，变量n又是0，但其实不然，第二次调用时n变为了1。分析一下：
 - `setN`一定会修改某个数据x，把`n+1`的值存入x；
 - `setN`一定会出发`App()`，重新渲染；
 - `useState`肯定会从数据x里读取n的最新值；
 - 那每个组件应该会有自己的一个数据x，暂时将其命名为`state`