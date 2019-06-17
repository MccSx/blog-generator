---
title: TypeScript基础
date: 2019-06-14 11:04:52
tags: 
 - JavaScript 
 - TypeScript
categories: TypeScript学习
---
# 变量
## 数据类型
 - JS七种数据类型（number、string、undefined、null、object、boolean、symbol）
 - any
```ts
    let n1:number = 1
    n1 = '1'  // error
    let n2:any = 2
    n2 = '2'  // pass
```
如果一个变量在赋值时声明了是`number`，后面如果把不是`number`的数据赋值给`n1`，那就会**报错**；如果一个变量在赋值时声明的时`any`，后面可以把任何类型的数据赋值给`n2`

<!-- more -->

 - 枚举(enum)
 ```ts
 enum Gender { Man, Woman }
 let jack:Gender = Gender.Man
 console.log(jack)  // => 0
 jack = Gender.Woman
 console.log(jack)  // => 1
 
 enum Gender1 = { Man='man', Woman='woman' }
 let jack:Gender1 = Gender1.Man
 console.log(jack)  // => 'man'
 jack = Gender1.Woman
 console.log(jack)  // => 'woman'
 ```
 
枚举用关键字`enum`来声明，默认情况下，元素编号从0开始，所以第一种情况的`jack`值默认是0。也可以手动指定成员的数据，就类似上述第二种情况。如果在给`jack`赋值时，不小心赋值了类似于`Gender.Men`，这样就会**报错**；
 - void
 代表空类型，一般用在没有返回值的函数声明时，类似于下述情况：
```ts
function a(b: any): void {
    console.log(a)
}
```
 - never

小tips：默认情况下，`null`或`undefined`是所有类型的子类型，就是说可以把`null`或`undefined`赋值给`number`、`string`等类型的变量。

## 类型断言
```ts
let str1: any = '123'
console.log( (<string>str1).split('') )  // => pass

let str2: any = '123'
console.log( (str2 as string).split('') )  // => pass
```
有两种形式：1.`<string>str`  2.`str2 as string`
通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。它没有运行时的影响，只是在编译阶段起作用。TypeScript会假设你，程序员，已经进行了必须的检查。

------
# 接口
接口就是用代码描述一个对象**必须**有什么属性或者方法，但是有没有其他属性或方法就不管。
```ts
interface Human {
    name: string;
    age: number;
}

let jack: Human = {name: 'jack', age: 18}
```
接口用关键字`interface`来声明，例如上述代码，如果`jack`对象声明时少了`name`或者`age`属性，就会报错。
```ts
interface Head {
    eye: number;
    nose: number;
}
interface Human {
    readonly name: string;
    age: number;
    head: Head;
    likedGame?: Array<string>
    say(word: string) : void
}

let jsck: Human = {
    name: 'jack',
    age: 18,
    head: {eye: 2, nose: 1},
    say(word: string) {
        console.log(word)
    }
}

jack.name = 'gavin'  // => error
```
- 如果一个接口内定义了必须还有对象，就类似上述再声明另一个接口；
- 如果一个接口内定义了必须有一个方法，就类似上述在接口内部直接定一个方法；
- 以上述代码为例，在接口定义时，在某一个属性前加一个`readonly`关键字，后面如果对`jsck`的`name`属性做修改就会**报错**；
- 如果定义一个接口时，在某个属性后面加一个`？`，表述对象声明时可以不声明这个对象，类似上述代码种的`likedGame`；