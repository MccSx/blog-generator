---
title: React造轮子-项目搭建
date: 2019-07-06 23:11:38
tags: 
 - TypeScript
 - React
categories: 
 - TypeScript学习
 - React
---
## 创建目录与远程仓库
在GitHub上新建一个远程仓库，创建时添加readme文件，然后使用`git clone`把远程仓库克隆到本地。

## npm初始化
进入到本地的项目目录，使用`npm init -y`命令，在本地新建一个`package.json`文件。

## 安装webpack
使用`yarn add webpack webpack-cli --dev`命令，`dev`的意思就是安装到开发者的依赖里边，也就是在`package.json`文件中多了一个`devDependenceies`属性。

## 新建lib/index.tsx文件
在项目目录下，新建一个lib目录，lib目录下新建一个`index.tsx`文件，在文件内先随意写一行log代码。

## 新建webpack.config.js
在项目目录下新建一个`webpack.config.js`文件，然后配置webpack：
```js
const path = require('path')
module.exports = {
    mode: 'production',
    entry: {
        index: './lib/index.tsx'
    },
    output: {
        path: path.resolve(__dirname, 'dist/lib'),
        libray: 'XinChen UI',
        librayTarget: 'umd'
    },
    modules: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'awesome-typescript-loader'
            }
        ]
    }
}
```
1. 配置`entry`，这表示项目代码入口在哪里，目前先定为lib目录下的`index.tsx`文件；
2. 配置`output`，这表示项目打包后所放置代码的位置，这里的`libray`表示项目最终的名称叫什么，`librayTarget`表示我们自己的库的格式是什么，；
3. 配置`mudules.rules`，首先要处理`.tsx`文件，配置了`awesome-typescript-loader`这个loader，配置完后需要安装这个loader：`yarn add awesome-typescript-loader --dev`(安装所有的loader都是后面加`--dev`，因为loader只有开发者用到)；
4. 安装typescript：`yarn add typescript --dev`，添加`tsconfig.json`和`tslint.json`文件;
5. 配置`mode`，一般`mode`值为`production`或者`development`，第一次先设置为`production`；