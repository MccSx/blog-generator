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

<!-- more -->

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
        library: 'XinChen UI',
        libraryTarget: 'umd'
    },
    module: {
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

## 安装webpack-dev-server与配置webpack.config.dev.js
首先安装webpack-dev-server：`yarn add webpack-dev-server --dev`，这样就可以使用命令`npx webpack-devserver`来启动项目；
安装html-webpack-plugin：`yarn add html-webpack-plugin --dev`，然后在`webpack.config.js`文件中添加下面代码：
```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
modules.exports = {
    // ...
    plugins: [
        new HtmlWebpackPlugin({
            title: 'XinChen UI',
            template: 'index.html'
        })
    ]
}
```
这样使用命令`npx webpack-dev-server`，浏览器会启动localhost:8080域名，打开项目目录下的index.html文件，并且这个html文件会自动使用lib目录下的index.tsx文件作为js文件。
接下来，在`package.json`文件内，`scripts`中添加如下两行代码：
```js
"start": "webpack-dev-server",
"build": "webpack"
```
这样我们启动项目是可以使用`yarn start`命令，打包项目可以使用`yarn build`命令。

## 使用React
首先安装`react`和`react-dom`：`yarn add react react-dom`，安装完成后，如果直接使用React，会报一个错误提示：
```js
无法找到模块“react”的声明文件。“/Users/xinchen/Public/self_work/XinChen-UI/node_modules/react/index.js”隐式拥有 "any" 类型。
Try `npm install @types/react` if it exists or add a new declaration (.d.ts) file containing `declare module 'react';`ts(7016)
```
错误提示很明显，没有安装`@types/react`，所以需要继续安装`@types/react`和`@types/react-dom`：`yarn add @types/react @types/react-dom --dev`。
在webpack.config.json文件内增加一个配置：
```js
modules.exports = {
    // ...
    resolve: {
        extensions: ['.ts', '.tsx', '.js'. '.jsx']
    }
}
```
这样，当我们引入上述四种文件时才能正常引用。
在`yarn start`的时候，会有一个警告，说index.js文件太大，这个需要在`webpack.config.json`文件中把`mode`值改为`development`，这样在开发模式下，不会管index.js是不是太大了。
```js
modules.exports = {
    // ...
    enternals: {
        react: {
            commonjs: 'react',
            commonjs2: 'react',
            amd: 'react',
            root: 'React',
        },
        'react-dom': {
            commonjs: 'react-dom',
            commonjs2: 'react-dom',
            amd: 'react-dom',
            root: 'ReactDom',
        }
    }
}
```
再在`webpack.config.json`文件中加入上述代码，意思就是告诉webpack，`enternals`内的是外部的库，这样打包的index.js文件就不会包含react的代码。

## 配置webpack.config.dev.js和webpack.config,prod.js
```js
// webpack.config.js
const path = require('path');

module.exports = {
    entry: {
        index: './lib/index.tsx'
    },
    output: {
        path: path.resolve(__dirname, './dist/lib'),
        library: 'XinChen UI',
        libraryTarget: 'umd'
    },
    resolve: {
        extensions: ['.ts', '.tsx', '.js', '.jsx']
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                loader: 'awesome-typescript-loader'
            }
        ]
    }
}
```
```js
// webpack.config.dev.js
const base = require('./webpack.config');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = Object.assign({}, base, {
    mode: 'development',
    plugins: [
        new HtmlWebpackPlugin({
            title: 'XinChen UI',
            template: 'index.html'
        })
    ],
})
```
```js
// webpack.config.prod.js
const base = require('./webpack.config');

module.exports = Object.assign({}, base, {
    mode: 'production',
    enternals: {
        react: {
            commonjs: 'react',
            commonjs2: 'react',
            amd: 'react',
            root: 'React',
        },
        'react-dom': {
            commonjs: 'react-dom',
            commonjs2: 'react-dom',
            amd: 'react-dom',
            root: 'ReactDom',
        }
    }
})
```
三个webpack配置代码如上述所示，`webpack.config.js`文件是共有的配置，`webpack.config.dev.js`是开发环境配置，`webpack.config.prod.js`文件是生产环境配置。
同时还需要修改`package.json`文件如下代码：
```js
"scripts": {
    //...
    "start": "webpack-dev-server --config webpack.config.dev.js",
    "build": "webpack --config webpack.config.prod.js"
},
```

## 打包生产.d.ts文件
在`tsconfig.json`文件这加入这么一行：`"outDir": "dist"`，这样重新打包后，dist目录下就会有`index.d.ts`和`Button.d.ts`文件（其实`Button.d.ts`文件是不需要的），那如何去掉这个文件呢，在这里先挖个坑。

## 配置Jest单元测试
1. 运行：
```js
yarn add --dev jest babel-jest @babel/preset-env @babel/preset-react react-test-renderer
```
2. 在根目录下添加`.babelrc`文件，在文件内添加如下代码：
```js
{
    "presets": [
        "react-app"
    ]
}
```
3. 在`package.json`文件内的scripts中添加如下一行代码：
```js
"test": "cross-env NODE_ENV=test jest --config=jest.config.js --runInBand"
```
4. 添加`jest.config.js`文件，在文件内添加如下代码：
```js
module.exports = {
    verbose: true,
    clearMocks: false,
    collectCoverage: false,
    reporters: ["default"],
    moduleFileExtensions: ['js', 'jsx', 'ts', 'tsx'],
    moduleDirectories: ['node_modules'],
    globals: {
      'ts-jest': {
        tsConfig: 'tsconfig.test.json',
      },
    },
    moduleNameMapper: {
      "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/test/__mocks__/file-mock.js",
    },
    testMatch: ['<rootDir>/**/__tests__/**/*.unit.(js|jsx|ts|tsx)'],
    transform: {
      "^.+unit\\.(js|jsx)$": "babel-jest",
      '^.+\\.(ts|tsx)$': 'ts-jest',
    },
    setupFilesAfterEnv: ["<rootDir>test/setupTests.js"]
}
```
5. 安装`ts-jest`：`yarn add ts-jest --dev`，并且创建test目录，并在这个目录下创建一个`setupTests.js`文件，目前这个文件暂时是空的。