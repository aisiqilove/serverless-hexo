---
title: 从零开始webpack4.x（二）基础
date: 2020-03-12 23:33
tags: webpack
---

## webpack基础

安装webpack

* 安装本地webpack
* yarn add webpack webpack-cli -D  或者使用npm

* 手动配置webpack
* 默认配置文件 webpack.config.js

``` JavaScript
let path = require('path');
let HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    mode: 'development', // 模式 默认2种 dev production development
    entry: './src/index.js', // 入口
    output: {
        filename: 'bundle.[hash:8].js', // 打包后的文件名  hash:number
        path: path.resolve(__dirname, 'dist'), // 打包后路径--绝对路径
    },
    plugins: [ // 数组 放所有webpack插件
        new HtmlWebpackPlugin({ // html输出文件
            template: './src/index.html',
            filename: 'index.html',
        })
    ],
    module: { // 模块
        rules: [ // 规则 css-loader 解析@import 这种语法
            // style-loader 把css 插入到head标签中
            // loader的特点 希望单一
            // loader用法 字符串只用一个loader
            // 多个loader用 []
            // loader执行顺序是 默认从右到左 从下到上
            // loader还可以写成对象 可以对一个参数options
        ]
    }
}
```

* package.json

``` json
{
    "name": "webpack",
    "version": "1.0.0",
    "main": "index.js",
    "license": "MIT",
    "scripts": {
        "build": "webpack --config webpack.config.js",  // 制定打包配置文件
        "dev": "webpack-dev-server"
    },
    "devDependencies": {
        "webpack": "^4.42.0",
        "webpack-cli": "^3.3.11",
        "html-webpack-plugin": "^3.2.0",
        "webpack-dev-server": "^3.10.3"
    }
}
```

* 入口文件

/src/index.html

``` html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>webpack</title>
    <style>
        .content {
            border: 1px solid #ddd;
            color: #333;
        }
    </style>
</head>

<body>
    <!-- 模版 -->
    <div class="content">webpack</div>
</body>

</html>
```

/src/index.js

``` JavaScript
let str = require('./a')
console.log(str)
console.log('hello world')
```

/src/a.js
``` JavaScript
module.exports = 'xxxx'
```

* webpack打包文件解析

``` JavaScript
(function(modules) { // webpackBootstrap
    // The module cache  先定一个缓存
    var installedModules = {};
    //  "./src/index.js" : {}
    // __webpack_require__方法返回 module.exports 
    // webpack实际上就是把我们需要解析的模块 a.js  index.js  ->  变成一个对象 然后通过微入口模块加载 模块之间递归的依赖关系 最后通过入口来运行

    // The require function   重写一个require
    function __webpack_require__(moduleId) { // "./src/index.js"

        // Check if module is in cache 
        if (installedModules[moduleId]) { //  不在缓存中
            return installedModules[moduleId].exports;
        }
        // Create a new module (and put it into the cache)
        var module = installedModules[moduleId] = {
            i: moduleId,
            l: false,
            exports: {}
        };

        // Execute the module function  // 从对象中找到 "./src/index.js" 的value函数
        modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

        // Flag the module as loaded
        module.l = true;

        // Return the exports of the module
        return module.exports;
    }

    ......

    // Object.prototype.hasOwnProperty.call
    __webpack_require__.o = function(object, property) {
        return Object.prototype.hasOwnProperty.call(object, property);
    };

    // __webpack_public_path__
    __webpack_require__.p = "";

    // Load entry module and return exports
    return __webpack_require__(__webpack_require__.s = "./src/index.js"); // 入口文件
})

({
    "./src/a.js": //  key  ->  模块的路径
        (function(module, exports) { // value  ->  函数
            eval("module.exports = 'xxxx'\n\n\n//# sourceURL=webpack:///./src/a.js?");
        }),

    "./src/index.js": (function(module, exports, __webpack_require__) {
        eval("let str = __webpack_require__(/*! ./a */ \"./src/a.js\")\nconsole.log(str)\nconsole.log('hello world')\n\n\n//# sourceURL=webpack:///./src/index.js?");
    })
});
```
