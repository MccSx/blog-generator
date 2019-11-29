---
title: React Hooks
date: 2019-07-14 23:43:00
tags:
  - React
categories:
  - React
---

# useState

## 使用状态

```js
const [n, setN] = React.useStaet(0);
const [user, setUser] = React.useState({ name: "F" });
```

- 注意事项：不可局部更新，地址要变

```jsx
function App() {
  const [user, setUser] = useState({ name: "Frank", age: 18 });
  const onClick = () => {
    setUser({
      name: "Jack"
    });
  };
  return (
    <div className="App">
      <h1>{user.name}</h1>
      <h2>{user.age}</h2>
      <button onClick={onClick}>Click</button>
    </div>
  );
}
```

<!-- more -->

如果是这样更新`user`对象，实际并未更新，需要重新一个对象传给`setUser`函数，实际上，`setState(obj)`如果`obj`地址不变，那么 React 就认为数据没有变化。通常以`setUser({...user, name: 'Jack'})`的方式来更新。因为`setState`不会帮我们合并属性，其实`useReducer`也不会合并属性。

## useState 接受函数

```jsx
const [state, setState] = useState(() => {
  return initialState;
});
```

该函数返回初始`state`，并且只执行一次

## setState 接受函数

```jsx
function App() {
  const [n, setN] = useState(0);
  const onClick = () => {
    setN(n + 1);
    setN(n + 1); // 你会发现 n 不能加 2
    // setN(i=>i+1)
    // setN(i=>i+1)
  };
  return (
    <div className="App">
      <h1>n: {n}</h1>

      <button onClick={onClick}>+2</button>
    </div>
  );
}
```

在这种时候，需要不止一次调用的时候，就需要传函数来实现，如果能接受这种形式，应该优先使用这种式。

# useReducer

## 使用 useReducer，用来践行 Flux/Redux 的思想

```jsx
const initial = {
  n: 0
};

const reducer = (state, action) => {
  if (action.type === "add") {
    return { n: state.n + action.number };
  } else if (action.type === "multi") {
    return { n: state.n * 2 };
  } else {
    throw new Error("unknown type");
  }
};

function App() {
  const [state, dispatch] = useReducer(reducer, initial);
  const { n } = state;
  const onClick = () => {
    dispatch({ type: "add", number: 1 });
  };
  const onClick2 = () => {
    dispatch({ type: "add", number: 2 });
  };
  return (
    <div className="App">
      <h1>n: {n}</h1>

      <button onClick={onClick}>+1</button>
      <button onClick={onClick2}>+2</button>
    </div>
  );
}
```

1. 创建初始值`initialState`；
2. 创建所有操作`reducer(state, action)`；
3. 传给`useReducer`，得到读和写 API；
4. 调用 => `({ type: '操作类型' })`
   总的来说，`useReducer`是`useState`的复杂版

## 如何代替 Redux

```jsx
const store = {
  user: null,
  books: null,
  movies: null
};

function reducer(state, action) {
  switch (action.type) {
    case "setUser":
      return { ...state, user: action.user };
    case "setBooks":
      return { ...state, books: action.books };
    case "setMovies":
      return { ...state, movies: action.movies };
    default:
      throw new Error();
  }
}

const Context = React.createContext(null);

function App() {
  const [state, dispatch] = useReducer(reducer, store);

  const api = { state, dispatch };
  return (
    <Context.Provider value={api}>
      <User />
      <hr />
      <Books />
      <Movies />
    </Context.Provider>
  );
}

function User() {
  const { state, dispatch } = useContext(Context);
  useEffect(() => {
    ajax("/user").then(user => {
      dispatch({ type: "setUser", user: user });
    });
  }, []);
  return (
    <div>
      <h1>个人信息</h1>
      <div>name: {state.user ? state.user.name : ""}</div>
    </div>
  );
}

function Books() {
  const { state, dispatch } = useContext(Context);
  useEffect(() => {
    ajax("/books").then(books => {
      dispatch({ type: "setBooks", books: books });
    });
  }, []);
  return (
    <div>
      <h1>我的书籍</h1>
      <ol>
        {state.books
          ? state.books.map(book => <li key={book.id}>{book.name}</li>)
          : "加载中"}
      </ol>
    </div>
  );
}

function Movies() {
  const { state, dispatch } = useContext(Context);
  useEffect(() => {
    ajax("/movies").then(movies => {
      dispatch({ type: "setMovies", movies: movies });
    });
  }, []);
  return (
    <div>
      <h1>我的电影</h1>
      <ol>
        {state.movies
          ? state.movies.map(movie => <li key={movie.id}>{movie.name}</li>)
          : "加载中"}
      </ol>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);

// 帮助函数

// 假 ajax
// 两秒钟后，根据 path 返回一个对象，必定成功不会失败
function ajax(path) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (path === "/user") {
        resolve({
          id: 1,
          name: "Frank"
        });
      } else if (path === "/books") {
        resolve([
          {
            id: 1,
            name: "JavaScript 高级程序设计"
          },
          {
            id: 2,
            name: "JavaScript 精粹"
          }
        ]);
      } else if (path === "/movies") {
        resolve([
          {
            id: 1,
            name: "爱在黎明破晓前"
          },
          {
            id: 2,
            name: "恋恋笔记本"
          }
        ]);
      }
    }, 2000);
  });
}
```

1. 将数据集中在一个`store`对象中；
2. 将所有操作集中在`reducer`；
3. 创建一个`Context`；
4. 创建对数据的读写 API；
5. 将第四步的内容放到第三步的`Context`；
6. 用`Context.Provider`将`Context`提供给所有组件；
7. 各个组件用`useContext`获取读写 API；

# useContext

## 上下文

`全局变量`是全局的`上下文`，`上下文`是局部的`全局变量`。

## 使用方法

```jsx
const C = createContext(null);

function App() {
  console.log("App 执行了");
  const [n, setN] = useState(0);
  return (
    <C.Provider value={{ n, setN }}>
      <div className="App">
        <Baba />
      </div>
    </C.Provider>
  );
}

function Baba() {
  const { n, setN } = useContext(C);
  return (
    <div>
      我是爸爸 n: {n} <Child />
    </div>
  );
}

function Child() {
  const { n, setN } = useContext(C);
  const onClick = () => {
    setN(i => i + 1);
  };
  return (
    <div>
      我是儿子 我得到的 n: {n}
      <button onClick={onClick}>+1</button>
    </div>
  );
}
```

1. 使用`C = createContext(initial)`创建上下文；
2. 使用`<C.provider>`圈定作用域；
3. 在作用域内使用`useContext(C)`来使用上下文；

## 注意事项

不是响应式，在一个模块降`C`里面的值改变，另一个模块不会感知到这个变化。

# useEffect

## 副作用

对环境的改变纪委副作用，如修改 document.title，但不一定非要吧副作用放在`useEffect`里，实际上叫做`afterRender`更好，每次 render 后运行。

## 用途

- 作为`componentDidMount`使用，[]作第二个参数；
- 作为`componentDidUpdate`使用，可指定依赖；
- 作为`componentWillUnmount`使用，用过 return；
  以上三种用途可以同时存在

## 特点

如果同时存在多个`useEffect`，会按照出现次序执行。

# useLayoutEffect

## 布局副作用

`useEffect`在浏览器渲染完成后执行，`useLayoutEffect`在浏览器渲染前执行。

## 特点

- `useLayoutEffect`总是比`useEffect`先执行
- `useLayoutEffect`里的任务最好影响了 Layout

## 经验

为了用户体验，`useLayoutEffect`其实会增加 DOM 的渲染时间，优先使用`useEffect`。

# useMemo

## 如何理解 useMemo

首先要理解`useMemo`，必须要先将`React.memo`，示例代码如下：

```jsx
function App() {
  const [n, setN] = React.useState(0);
  const [m, setM] = React.useState(0);
  const onClick = () => {
    setN(n + 1);
  };

  return (
    <div className="App">
      <div>
        <button onClick={onClick}>update n {n}</button>
      </div>
      {/* <Child data={m} /> */}
      <Child2 data={m} />
    </div>
  );
}

function Child(props) {
  // 假设这里有大量代码
  console.log("child 执行了");
  return <div>child: {props.data}</div>;
}

const Child2 = React.memo(Child);
```

这样使用了`React.memo`的`Child2`组件，在`n`变化时，`Child2`组件并不会重新`render`。
但是，`React.memo`还是有一个问题，改造上述代码如下：

```jsx
function App() {
  // ...
  const onClickChild = () => {
    console.log(m);
  };

  retrun(
    <div className="App">
      <Child2 data={m} onClick={onClickChild} />
    </div>
  );
}

// ...
```

这样的话，即使`Child2`组件使用了`React.memo`，`Child2`组件还是会重新`render`，原因其实就是`onClickChild`被赋值了一个新的函数，新旧函数功能一样，但是地址是不一样的。
那如何解决这样的问题呢？所以出现了`useMemo`，修改代码如下：

```jsx
function App() {
  // ...
  const onClickChild = useMemo(() => {
    const fn = div => {
      console.log("on click child, m: " + m);
      console.log(div);
    };
    return fn;
  }, [m]);

  retrun(
    <div className="App">
      <Child2 data={m} onClick={onClickChild} />
    </div>
  );
}

// ...
```

## useMemo 特点

- 第一个参数是一个函数：`() => value`；
- 第二个参数是依赖`[m,n]`；
- 只有当依赖变化是，才会计算出新的`value`；
- 如果依赖不变，那么就重用之前的`value`；

## useMemo 注意点

如果上述`value`是一个函数，那可能就会写成这样：`useMemo(() => (x) => console.log(x))`，这是一个返回函数的函数。那会不会觉得很难用呢？于是出现了`useCallback`。

# useCallback

修改上述代码，使用`useCallback`来代替`useMemo`。

```jsx
// const onClickChild = useMemo(() => {
//   const fn = div => {
//     console.log("on click child, m: " + m);
//     console.log(div);
//   };
//   return fn;
// }, [m]);

const onClickChild = useMemo(() => {
  console.log("on click child, m: " + m);
}, [m]);
```

`useCallback(x => log(x), [m])`等价于 useMemo`(() => x => log(x), [m])`。
