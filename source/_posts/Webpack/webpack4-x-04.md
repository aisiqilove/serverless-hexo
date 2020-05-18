---
title: 从零开始webpack4.x（四）样式loader
date: 2020-03-13 00:19
tags: webpack
---

## 样式loader

依赖：style-loader css-loader 如果有less sass下载对应loader (sass-loader可以换成sassjs-loader 解决node-sass sass-loader 下载失败问题)

``` js
module: { // 模块
    rules: [ // 规则 css-loader 解析@import 这种语法
        // style-loader 把css 插入到head标签中
        // loader的特点 希望单一
        // loader用法 字符串只用一个loader
        // 多个loader用 []
        // loader执行顺序是 默认从右到左 从下到上
        // loader还可以写成对象 可以对一个参数options
        {
            test: /\.css$/,
            use: [{
                    loader: 'style-loader',
                    options: {
                        insertAt: 'top'
                    }
                },
                'css-loader',

            ]
        },
        { // sass stylus node-sass sass-loader 
            test: /\.less$/,
            use: [{
                    loader: 'style-loader',
                    options: {
                        insertAt: 'top'
                    }
                },
                'css-loader', // 顺序不能变 默认从下到上执行
                'less-loader' // 把less -> css
            ]
        }
    ]
}
```

如果希望抽离css，自动添加浏览器前缀 -webkit-等
依赖：mini-css-extract-plugin  postcss-loader  autoprefixer  @babel/plugin-proposal-class-properties 
@babel/plugin-proposal-decorators  @babel/plugin-syntax-class-properties   @babel/plugin-transform-runtime

``` js
let MiniCssExtractPlugin = require('mini-css-extract-plugin'); // 抽离css插件
let OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin'); // 抽离css后压缩优化
let TerserJSPlugin = require('terser-webpack-plugin'); // 配合压缩js
module.exports = {
    optimization: { // 优化项
        minimizer: [
            new TerserJSPlugin(),
            new OptimizeCSSAssetsPlugin({})
        ],
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: 'main.css' // 生成等css文件名
        })
    ],

    module: {
        rules: [{
            test: /\.css$/,
            use: [
                MiniCssExtractPlugin.loader,
                'css-loader',
                'postcss-loader'
            ]
        }]
    }
}
```

使用postcss-loader 需要添加 postcss.config.js
``` js 
module.exports = {
  plugins: [

    require('autoprefixer')  // 添加浏览器前缀

] }

``` 
然后在package.json中"devDependencies"下添加

``` js

"browserslist": [

        "defaults",
        "not ie <= 8",
        "last 2 versions",
        "> 1%",
        "iOS >= 7",
        "Android >= 4.0"

  ]
```
