---
title: 从零开始webpack4.x（九）watch、resolve、小插件、跨域问题、环境变量、不同环境打包
date: 2020-04-12 16:32
tags: webpack
---

## watch用法

实时打包

webpack.config.js

``` js
module.exports = {
    watch: true,
    watchOptions: { // 监控
        poll: 1000, // 每秒 1000次
        aggregateTimeout: 500, // 防抖
        ignored: /node_modules/ // 不需要监控的文件夹
    }
}
```

## resolve

``` js
module.exports = {
    resolve: { // 解析 第三方包 common
        modules: [path.resolve('node_modules')],
        extensions: ['.js', '.css', '.json', '.vue'], // import xx from 'xxx.vue' 依次查询文件后缀
        // mainFields: ['style', 'main'],  // 主入口
        // mainFiles: [],  // 入口文件 index.js
        // alive: { // 别名 vue  vue.runtime
        //     bootstrap: 'bootstrap/dist/css/bootstrap.css'
        // }
    }
}
```

## webpack小插件

* CleanWebpackPlugin 清除文件夹
* copyWebpackPlugin 拷贝插件
* bannerPlugin 内置 插入版权

``` js
module.exports = {
    let {
        CleanWebpackPlugin
    } = require('clean-webpack-plugin');
    let CopyWebpackPlugin = require('copy-webpack-plugin');
    let webpack = require('webpack');

    plugins: [
        new CleanWebpackPlugin(), // 清除打包文件夹
        new CopyWebpackPlugin([ // 拷贝插件
            {
                from: 'doc',
                to: './'
            }
        ]),
        new webpack.BannerPlugin('make 2020 0408')
    ]
}
```

## webpack跨域问题

``` js
module.exports = {
    devServer: {
        // 1）重写方法 把请求代理到express服务器上
        // proxy: {
        //     '/api': {
        //         target: 'http://localhost:3000',
        //         pathRewrite: {'/api': ''}  // 替换api
        //     }  // 配置一个代理   
        // }

        // 2）前端单纯模拟数据
        // before(app) {
        //     app.get("/user", (req, res) => {
        //         res.json({ name: "chongzeng2"})
        //     })
        // }

        // 3）有服务端 不用代理 在服务端启动webpack 端口用服务端端口
    }
}
```

sever.js

``` js
let express = require("express");

let app = express();
let webpack = require("webpack");
// 中间件
let middle = require("webpack-dev-middleware");

// 配置
let config = require("./webpack.config");

// 编译后
let compiler = webpack(config);

// 使用 开启服务的时候 同时启动webpack
app.use(middle(compiler));

// 然后在终端 启动服务 node server.js 可以通过3000访问到接口
app.get("/user", (req, res) => {
    res.json({
        name: "chongzeng"
    })
})

app.listen(3000);
```

index.js

``` js
let xml = new XMLHttpRequest();

xml.open('GET', '/api/user', true); // 1) proxy
xml.open('GET', '/user', true); //  2) 前端模拟数据

xml.onload = (() => {
    console.log(xml.response);
})

xml.send();
```

## webpack环境变量

webpack. DefinePlugin

``` js
module.exports = {
    plugins: [
        new webpack.DefinePlugin({ // 定义环境变量
            DEV: JSON.stringify('dev'), // console.log('dev')
            FLAG: 'true',
            EXPORESSION: '1+1'
        })
    ]
}
```

## webpack区分不同环境

创建不同环境的配置文件 使用webpack-merge 合并webpack不同环境配置文件

通过npx webpack --config 相应环境配置文件 打包 或者配置package.json中的 scripts

webpack.dev.js

``` js
// webpack-merge 合并webpack 不同环境配置文件
let {
    smart
} = require('webpack-merge');
let base = require('./webpack.base');

module.exports = smart(base, {
    mode: 'development'
    其他配置
    ....
})
```

webpack.prod.js

``` js
let {
    smart
} = require('webpack-merge');
let base = require('./webpack.base');

module.exports = smart(base, {
    mode: 'production',
    entry: {},
    output: {},
    devServer: {},
    module: {},
    plugins: [],
})
```
