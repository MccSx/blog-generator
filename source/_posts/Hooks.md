---
title: React Hooks
date: 2019-07-14 23:43:00
tags:
  - React
categories:
  - React
---
## useState
 - 使用状态
 ```js
 const [n, setN] = React.useStaet(0);
 const [user, setUser] = React.useState({name: 'F'})
 ```
 - 注意事项：不可局部更新，地址要变
```jsx
function App() {
  const [user,setUser] = useState({name:'Frank', age: 18})
  const onClick = ()=>{
    setUser({
      name: 'Jack'
    })
  }
  return (
    <div className="App">
      <h1>{user.name}</h1>
      <h2>{user.age}</h2>
      <button onClick={onClick}>Click</button>
    </div>
  );
}
```
     如果是这样更新`user`对象，实际并未更新，需要重新一个对象传给`setUser`函数，实际上，`setState(obj)`如果`obj`地址不变，那么React就认为数据没有变化。通常以`setUser({...user, name: 'Jack'})`的方式来更新。因为`setState`不会帮我们合并属性，其实`useReducer`也不会合并属性。
 - useState接受函数
```jsx
const [state, setState] = useState(() => {
  return initialState
})
```
    该函数返回初始`state`，并且只执行一次
 - setState接受函数
```jsx
function App() {
  const [n, setN] = useState(0)
  const onClick = ()=>{
    setN(n+1)
    setN(n+1) // 你会发现 n 不能加 2
    // setN(i=>i+1)
    // setN(i=>i+1)
  }
  return (
    <div className="App">
      <h1>n: {n}</h1>
       
      <button onClick={onClick}>+2</button>
    </div>
  );
}
```
    在这种时候，需要不止一次调用的时候，就需要传函数来实现，如果能接受这种形式，应该优先使用这种形式。