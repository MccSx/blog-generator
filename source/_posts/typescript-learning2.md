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
## readonly修饰符
```ts
class Human {
    name: string;
    readonly age: number;
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    };
}

let jack = new Human('jack', 18)
jack.age = 8  // 报错，age是只读的
```
用`readonly`关键字将属性设置为只读，只读属性必须在声明时或这早函数里被初始化。
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
## 抽象类
抽象类也可以叫做“爸爸类”：专门当作别的类的爸爸的类；也可以叫做“没有写完的类”：只描述有什么方法，并没有完全实现这些方法。
```ts
abstract class Animal {
    abstract makeNoice(): void {};
    move(): void {
        // ...
    };
}
let animal = new Animal() // 报错，因为Animal是抽象类，makeNoice方法并没有写完

class Human extends Animal {
    name: string;
    age: number;
    constructor(name: string, age: number) {
        super();
        this.name = name;
        this.age = age;
    };
    makeNoice(): void {
        console.log('说普通话');
    }
}

let jack = new Human('jack', 18)
```
1. 抽象类要在声明时前面加`abstract`修饰符；
2. 抽象类内部有一个方法，这个方法并不能在当时写全，那这个方法前面加一个`abstract`修饰符；
3. 由如上述代码中，`new Animal()`实例化会报错，因为内部有方法并没有写全；
4. 父类中如果有未写完的方法，那子类（非抽象类）中需要写完它；