---
title: TypeScript-泛型
date: 2019-06-20 23:18:18
tags: 
 - JavaScript 
 - TypeScript
categories: TypeScript学习
---
# 泛型
## 泛型的使用
```ts
function returnIt<T>(sth: T): T {
    return sth;
};

let s = returnIt<string>('hi');
let s2 = returnIt<number>('hello') // 报错了
```
**泛型**就是用一个东西表示广泛的类型，由如上述代码，在函数变量名`returnIt`后面加`<T>`，然后函数参数指定`T`类型，函数返回也是`T`类型，**并且这个`T`可以写任意字母**。`returnIt<number>('hello')`会报错，因为`'hello'`是`string`类型，但是函数调用时说明了接受`number`类型参数。

<!-- more -->

```ts
function returnIt<T>(sth: T): T {
    return sth;
};
interface Human {
    name: string;
    age: number;
}

let s = returnIt<Human>({name: 'jack', age: 18})
let s1 = returnIt({name: 'jack', age: 18})
let s2 = returnIt<Human>({name: 'jack'})  // 报错
```
泛型和接口一起使用，由如上述代码，`s` `s1`都可以，`s2`会报错，因为传入函数的参数不符合接口的定义。

## 使用泛型变量
```ts
function returnArray<T>(array: T[]): T[] {
    return array;
};

let s = returnArray([1,2,3]);
let s1 = returnArray('hi'); // 报错，因为'hi'不是一个数组
```
使用泛型类型，可以说明函数参数是一个广泛类型的数组（不关心这个数组内部是什么类型）。

## 泛型类
```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```
这个是官方文档的一个例子，当`myGenericNumber`实例化时，`myGenericNumber`内就有一个`number`类型的`zeroValue`属性，还有一个`add`方法，这个方法接收两个都是`number`类型的参数，返回值也是`number`类型。

## 泛型约束
```ts
// 1
function returnIt<T>(sth: T): T {
    console.log(sth.length);  // 报错，sth没有length属性
    return sth;
};
let s = returnIt<string>('hi');

// 2
interface hasLength {
    length: number;
}
function returnIt<T extends hasLength>(sth: T): T {
    console.log(sth.length);
    return sth;
};

let s = returnIt<string>('hi');
```
上述代码第一种情况下，泛型T并没有`length`属性，所以当访问`length`属性时，自然会报错，这就是对泛型的约束。第二种情况下，定义了一个具有`length`属性的接口，并且泛型T继承与接口`hasLength`，这样就不会报错了。