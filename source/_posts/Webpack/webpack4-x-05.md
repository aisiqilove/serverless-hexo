---
title: 从零开始webpack4.x（五）js处理
date: 2020-03-13 00:40
tags: webpack
---

## 语法转换 -> babel-loader

依赖： babel-loader @babel/core @babel/preset-env @babel/polyfill 其他依赖可以通过babeljs官方网站查询，转换装饰器 class语法

``` js
module.exports = {
    module: {
        rules: [{
            test: /\.js$/,
            use: {
                loader: 'babel-loader',
                options: {
                    presets: [
                        '@babel/preset-env' // 转换es6 -> es5
                    ],
                    plugins: [
                        ["@babel/plugin-proposal-decorators", {
                            "legacy": true
                        }], // 转换@装饰器
                        ["@babel/plugin-proposal-class-properties", {
                            "loose": true
                        }], // 转换class语法
                        ["@babel/plugin-transform-runtime"] // 特殊语法'aa'.includes('a'); 在require('@babel/polyfill')
                    ]
                }
            },
            include: path.resolve(__dirname, 'src'), // 包含
            exclude: /node_modules/ // 排除文件夹
        }]
    }
}
```

## js校验 

依赖：eslint eslit-loader  

需要添加文件'.eslintrc.js' 可以去[eslint](https://eslint.bootcss.com/) 官网下载一个配置

``` js
    module: {
        rules: [{ // eslint
            test: /\.js$/,
            use: {
                loader: 'eslint-loader',
                options: {
                    enforce: 'pre' // 强制前置执行 previous   post
                }
            }
        }]
    }
```
