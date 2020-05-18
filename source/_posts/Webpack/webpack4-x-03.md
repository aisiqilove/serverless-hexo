---
title: 从零开始webpack4.x（三）html插件
date: 2020-03-12 23:35
tags: webpack
---

## html-webpack-plugin

主要有两个作用

1. 为html文件中引入的外部资源如script、link动态添加每次compile后的hash，防止引用缓存的外部文件问题

2. 可以生成创建html入口文件，比如单页面可以生成一个html文件入口，配置N个html-webpack-plugin可以生成N个页面入口

webpack.config.js

``` js
let HtmlWebpackPlugin = require('html-webpack-plugin');

plugins: [
    new HtmlWebpackPlugin({ // 输出html文件
        template: './src/index.html', // html模板所在的文件路径
        filename: 'index.html', // 输出文件名
        minify: { // 压缩
            removeAttributeQuotes: true, // 去掉 ""
            collapseWhitespace: true // 去掉空格
        },
        hash: true // 每次打包都有不同的hash戳
    }),
    new MiniCssExtractPlugin({
        filename: 'main.css'
    })
]
```

一些常用的minify配置

``` js
plugins: [
    new HtmlWebpackPlugin({
        //部分省略，具体看minify的配置
        minify: {
            //是否对大小写敏感，默认false
            caseSensitive: true,

            //是否简写boolean格式的属性如：disabled="disabled" 简写为disabled  默认false
            collapseBooleanAttributes: true,

            //是否去除空格，默认false
            collapseWhitespace: true,

            //是否压缩html里的css（使用clean-css进行的压缩） 默认值false；
            minifyCSS: true,

            //是否压缩html里的js（使用uglify-js进行的压缩）
            minifyJS: true,

            //Prevents the escaping of the values of attributes
            preventAttributesEscaping: true,

            //是否移除属性的引号 默认false
            removeAttributeQuotes: true,

            //是否移除注释 默认false
            removeComments: true,

            //从脚本和样式删除的注释 默认false
            removeCommentsFromCDATA: true,

            //是否删除空属性，默认false
            removeEmptyAttributes: true,

            //  若开启此项，生成的html中没有 body 和 head，html也未闭合
            removeOptionalTags: false,

            //删除多余的属性
            removeRedundantAttributes: true,

            //删除script的类型属性，在h5下面script的type默认值：text/javascript 默认值false
            removeScriptTypeAttributes: true,

            //删除style的类型属性， type="text/css" 同上
            removeStyleLinkTypeAttributes: true,

            //使用短的文档类型，默认false
            useShortDoctype: true,
        }
    }),
]
```
