---
title: 从零开始webpack4.x（七）图片处理及打包文件分类
date: 2020-04-08 14:56
tags: webpack
---

## Webpack图片处理

1. js中引用图片
2. css中引用图片
3. html中引用图片

前面2种方法对应 file-loader  url-loader
html中需要html-withimg-loader

webpack.config.js

``` js
module.exports = {
    module: { // 模块
        rules: [{
            test: /\.(htm|html)$/i,
            loader: 'html-withimg-loader'
        }, {
            test: /\.(png|jpg|gif|jpeg)$/,
            // 做一个限制 如果图片 小于多少k 用base64转换 同时减少http请求 但图片会比原来大三分之一
            // 否则用file-loader产生真实的图片
            use: {
                loader: 'url-loader',
                options: {
                    limit: 200 * 1024,
                    esModule: false // 4.2版本file-loader的时候是没事的，现在file-loader升级到5.0 需要加上
                }
            }
        }],
        ....
    }
}
```

## 打包文件分类

css文件 image文件 js文件
全局公共路径在output中添加 publicPath
webpack.config.js

``` js
module.exports = {
    output: {
        filename: 'bundle.[hash:8].js',
        path: path.resolve(__dirname, 'dist'),
        // publicPath: ''  //  全局添加路径 公共路径
    },
    plugins: [webpack插件
        ...
        new MiniCssExtractPlugin({
            filename: 'css/main.css' // 加上css目录
        })

    ],
    module: { // 模块
        rules: [{
            test: /\.(png|jpg|gif|jpeg)$/,
            use: {
                loader: 'url-loader',
                options: {
                    limit: 1,
                    outputPath: 'img/', // 1. img文件夹下
                    // outputPath: '/img/', // 2. 加上cdn
                    // publicPath: 'https://www.baidu.com',  //  img 添加cdn加速 路径
                    esModule: false
                }
            }
        }, ],
        ....
    }
}
```
