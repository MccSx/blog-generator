---
title: typescript-类
date: 2019-06-19 22:58:05
tags: 
 - JavaScript 
 - TypeScript
categories: TypeScript学习
---
# 类
## 类的声明
```ts
class Human {
    name: string;
    age: number;
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    };
    move() {
        console.log('可以行走...')
    };
}

let jack = new Human('jack', 18)  // jack ==> {'jack', 18}
```
类的声明用`class`修饰符，内部可以使用构造器`constructor`，构造器`constructor`是一个函数，可以让我们用`new ...`来实例化一个对象，并且`constructor`的参数可以设置默认值；`Human`类内部可以定义方法，类似上述`move`函数。
<!-- more -->
## 类的静态属性
```ts
class Human {
    static a = 1;
    name: string;
    age: number;
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    };
}

let jack = new Human('jack', 18)
```
类的静态属性用`static`修饰符，每个实例想要访问这个属性的时候，都要类似于`jack.a`来访问。
## 类的私有属性
```ts
class Human {
    name: string;
    age: number;
    private secret: string;
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
        this.secret = '这是我的秘密';
    };
}

let jack = new Human('jack', 18)
console.log(jack.secret)  // ==> 报错
```
类的私有属性使用`private`修饰符，`jack`访问`secret`属性时会报错，`secret`属性只能在`Human`类种使用。这里增加一点说明，`name`和`age`属性默认是`public`，其实是这样：`public name: string;` `public age: number;`。
## 类的继承
```ts
class Animal {
    type: string;
    constructor(type: string) {
        this.type = type;
    };
    move(): void {
        // ...
    };
}
class Human extends Animal {
    name: string;
    age: number;
    constructor(name: string, age: number) {
        super('哺乳动物');
        this.name = name;
        this.age = age;
    };
}

let jack = new Human('jack', 18)  // jack ==> {'jack', 18}
```
类的继承也是用修饰符`extends`来实现，**并且**，`Human`继承`Animal`，在`Human`的`constructor`内需要调用`super()`；上述代码中，`super('哺乳动物')`相当于调用了`Animal`种的`constructor()`，这样`jack`就有了`type`属性，并且`jack.type === '哺乳动物'`。
## 理解protected
```ts
class Animal {
    type: string;
    protected hand: boolean;
    constructor(type: string, hand: boolean) {
        this.type = type;
        this.hand = hand
    };
    move(): void {
        // ...
    };
}
class Human extends Animal {
    name: string;
    age: number;
    constructor(name: string, age: number) {
        super('哺乳动物', true);
        this.name = name;
        this.age = age;
    };
}

let jack = new Human('jack', 18)
console.log(jack.hand)  // ==> 报错
```
`protected`修饰符雨`private`类似，但有一点不同，`protected`成员在它的子类仍然可以使用，而`private`就不可以。