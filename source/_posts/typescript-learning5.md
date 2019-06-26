---
title: TypeScript+Vue入门
date: 2019-06-26 15:36:35
tags: 
 - JavaScript 
 - TypeScript
 - Vue
categories: TypeScript学习
---
## 工程创建
1. 如果没有安装Vue cli，需要先安装：`npm install --global @vue/cli`;
2. 创建一个新工程，如果已经在自己手动创建好的目录下：`vue create .`，如果没有手动创建目录：`vue create my-project-name`;
3. 工程构建时，提示`Check the features needed for your project`时，选择了`Babel、TypeScript、CSS Pre-processors`;
4. 提示`Use class-style component syntax?`时，选择yes；

## 工程目录部分结构不同点
src目录下，有三个`.ts`文件`main.ts` `shims-tsx.d.ts` `shims-vue.d.ts`，`main.ts`依然是入口文件，`shims-tsx.d.ts`文件主要方便在ts中使用jsx语法，`shims-tsx.d.ts`文件用于ts识别`.vue`文件；

## 基础语法
```java
<template>
  <div id="app">
    <input v-model="msg">
    <p>prop: {{propMessage}}</p>
    <p>msg: {{msg}}</p>
    <p>helloMsg: {{helloMsg}}</p>
    <p>computed msg: {{computedMsg}}</p>
    <button @click="greet">Greet</button>
    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
  </div>
</template>

<script>
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from './components/HelloWorld.vue';

@Component({
  props: {
    propMessage: String
  },
  components: {
    HelloWorld,
  },
})
export default class App extends Vue {
  // initial data
  msg = 123

  // use prop values for initial data
  helloMsg = 'Hello, ' + this.propMessage

  // lifecycle hook
  mounted () {
    this.greet()
  }

  // computed
  get computedMsg () {
    return 'computed ' + this.msg
  }

  // method
  greet () {
    alert('greeting: ' + this.msg)
  }
}
</script>
```
1. `Component`和`Vue`从`vue-property-decorator`引入；
2. `props`和`components`都放在`@Component`里边声明；
3. 初始数据直接使用`msg = 123`的形式；
4. 方法、钩子函数等都放在类似上述代码`App`对象内；