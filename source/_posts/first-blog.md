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
