---
title: 从零开始webpack4.x（十）noParse、IgnorePlugin、happypack
date: 2020-04-12 17:01
tags: webpack
---
 

## noParse

不解析模块中的依赖关系 提高打包效率

webpack.config.js

``` js
module.exports = {
    module: {
        noParse: /jquery/, // 不解析模块中的依赖关系 提高打包效率
        rules: [{
            test: /\.js$/,
            use: {
                loader: 'babel-loader',
                options: {
                    presets: [
                        '@babel/preset-env',
                        '@babel/preset-react'
                    ]
                }
            }
        }]
    }
}
```

## IgnorePlugin

忽略第三方包指定目录，让这些指定目录不要被打包进去

webpack.config.js

``` js
module.exports = {
    plugins: [
        new webpack.IgnorePlugin(/\.\/locale/, /moment/), // 在 moment 引入.locale 语言包时 忽略  减小打包体积      
    ]
}
```

index.js

``` js
// import moment from 'moment';

// // 手动引用中文包
// import  "moment/locale/zh-cn";
// // 设置语言包
// moment.locale('zh-cn');   // 中文

// let date = moment().endOf('day').fromNow(); 
// console.log(date)
```

## happypack

多线程打包

无需多线程时，尽量不要使用happypack 会消耗打包性能

``` js
let path = require('path');
let HtmlWebpackPlugin = require('html-webpack-plugin');
let webpack = require('webpack');
let Happypack = require('happypack');
// 多线程打包  happypack
module.exports = {
    mode: 'production',
    entry: {
        home: './src/home.js'
    },
    output: {
        filename: 'home.js',
        path: path.resolve(__dirname, 'dist')
    },
    plugins: [
        new Happypack({
            id: 'js',
            use: [{
                loader: 'babel-loader',
                options: {
                    presets: [
                        '@babel/preset-env',
                        '@babel/preset-react'
                    ]
                }
            }]
        }),
        new HtmlWebpackPlugin({
            template: './index.html',
            filename: 'index.html'
        })
    ],
    module: {
        rules: [{
            test: /\.js$/,
            // exclude: /node_modules/,     // 优化打包文件
            // include:path.resolve('src'), // 优化打包文件
            use: 'Happypack/loader?id=js' // 多线程 id匹配js  css也可以多线程打包
        }]
    }

}
```
