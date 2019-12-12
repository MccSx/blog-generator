---
title: JavaScript函数理解
date: 2019-12-12 17:55:07
tags:
  - JavaScript
categories:
  - JavaScript
---

# 函数定义

可以叫作为**子程序**（routine），一个或多个语句组成，完成特定的任务，相对独立。其中函数可以分为三种，**函数 function**：有返回值，**过程 procedure**：无返回值，**方法 method**：在类或对象中。
函数的返回值由两个因素决定，一个是**调用时**输入的参数 params，一个是**定义时**的环境 env。

# 声明一个函数

```js
// 1
const f1 = new Function("a", "b", "return a+b");

// 2
function f2(a, b) {
  return a + b;
}

// 3
const f3 = function(a, b) {
  return x + b;
};

// 4
const f4 = (a, b) => a + b;
```

其中，f1，f2，f3 是 ES6 之前的语法，支持`this/arguments/new`，而 f4 是 ES6 新语法，不支持`this/arguments/new`，注意：这里的不支持不是说在箭头函数内不用使用。用如下代码解释：

```js
// 1
const a = 1;
const f1 = () => console.log(a);

// 2
console.log(this);
const f2 = () => console.log(this);
```

箭头函数如何处理啊，就如何处理`this`。即：箭头函数把`this`当做外部的变量，仅此而已，但是非箭头函数的`this`就有很多**特殊处理**。所以，箭头函数不支持 this 值得就是**箭头函数对 this 与其他变量一视同仁，不会特殊对待**。
