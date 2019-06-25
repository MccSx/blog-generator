---
title: TypeScript-函数
date: 2019-06-20 17:14:01
tags: 
 - JavaScript 
 - TypeScript
categories: TypeScript学习
---
# 函数
函数其实也是对象，只是是一个特殊的对象，是**可以被调用（call）的对象**。
## 函数类型
```ts
function add(x: number, y: number): number {
    return x + y;
}

ley add2 = function(x: number, y: number): number {
    return x + y;
}
```
给每个参数添加类型之后再为函数本身添加返回值的类型，ts可以根据返回语句自动推断出返回值类型，如果实际返回值不是规定的类型就会报错。
<!-- more -->
```ts
let add2: (x: number, y: number) => number = function(x: number, y: number): number {
    return x + y;
}
```
上述代码是表示**完整函数类型**，其实有两部分，等于号前面和后面两部分；等于号前面部分，意思就是给`add2`函数定义了一个函数类型，是一个接收两个`number`类型的参数并且返回值是`number`类型的函数类型；等于号后面就是一个匿名函数，这个函数也是符合前面定义的函数类型的函数。上述代码中，`let add2: (x: number, y: number) => number`其实是这样的：`let add2: ((x: number, y: number) => number)`
```ts
let add2: (x: number, y: number) => number = function(x, y): {
    return x + y;
}
```
如果在赋值语句的一边指定了类型，但是另一边没有类型的话，ta编译器会自动识别出类型。

## 可选参数和默认参数
```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + " " + lastName;
    } else {
        return firstName;
    }
}

let res1 = buildName('Bob');
let res2 = buildName('Bob', 'Adams');
```
对于可选参数，可以在参数后面加一个问号，如上述代码`lastName?: string`。
```ts
function buildName(firstName: string, lastName='Smith') {
    return firstName + " " + lastName;
}

let res1 = buildName('Bob');  // Bob Smith
let res2 = buildName('Bob', 'Adams');  // Bob Adams
let res3 = buildName('Bob', undefined)  // Bob Smith
```
在ts里，可以为参数提供一个默认值（当用户没有传值或者传了undefined）。

## 剩余参数
```ts
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + ' ' + restOfName.jion(' ')
}

let fullName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```
剩余参数会被当做个数不限的可选参数，可以一个都没有，同样也可以有任意个；在参数前加`...`，表示一个数组，冒号后面可以定义是个`string`类型数组还是`number`类型数组或其他类型数组；在函数体内可以使用这个数组。

## this和箭头函数
this的值在函数被调用的时候才确定，也就是说，**this值其实是这个函数的参数，只有调用这个函数时才知道this的值是什么**；在TS里，this本质还是和JS里的this一样，不过在TS里，可以事先给this指定类型，或指定必须符合哪个接口或是哪个类：
```ts
interface Human {
    name: string;
    age: number;
}
function fn(this: Human) {
    console.log(this);
}

fn.call({name: 'jack', age: 18});  // pass
fn();  // error
```

## 函数的重载
```ts
function add(n1: number, n2: number);
function add(n1: string, n2: string);
function add(n1, n2) {
    return n1 + n2;
}

add(1, 2);  // pass  => 3
add('a', 'b')  // pass  => 'ab'
add(1, '2')  // error 两个参数不符合同时是string类型或者number类型
```
上述代码中，第一行和第二行表示对函数参数类型的定义，可以同时是`number``，或者同时是`string`，第三行开始的函数就是表示函数功能的具体实现。

## 拓展
#### 类型推断
```ts
function add(n1: string, n2: string) {
    return n1 + n2;
}

let s = add('a' + 'b');
console.log(s.split(''));  // pass
```
add函数在声明时并不需要声明返回值时什么类型，因为TS自己会推断函数的返回值是什么类型，所以当`s.split('')`时不会报错。
#### 类型兼容
```ts
interface Human {
    name: string;
    age: number;
}

let human1: Human = {name: 'jack', age: 18, gender: 'male'}; //报错，因为对象不符合接口Human的定义，多了gender属性

let x = {name: 'jack', age: 18, gender: 'male'};
let human2: Human = x;  //这里不会报错，pass
```
至于这样设计的目的，应该是可以让我们少些一些代码，节约类型的个数。
#### Unsound
```typescript
enum EventType { Mouse, Keyboard }

interface Event { timestamp: number; }
interface MouseEvent extends Event { x: number; y: number }
interface KeyEvent extends Event { keyCode: number }

function listenEvent(eventType: EventType, handler: (n: Event) => void) {
    /* ... */
}

// Unsound, but useful and common
listenEvent(EventType.Mouse, (e: MouseEvent) => console.log(e.x + ',' + e.y));

// Undesirable alternatives in presence of soundness
listenEvent(EventType.Mouse, (e: Event) => console.log((<MouseEvent>e).x + ',' + (<MouseEvent>e).y));
listenEvent(EventType.Mouse, <(e: Event) => void>((e: MouseEvent) => console.log(e.x + ',' + e.y)));

// Still disallowed (clear error). Type safety enforced for wholly incompatible types
listenEvent(EventType.Mouse, (e: number) => console.log(e));  // 报错
```