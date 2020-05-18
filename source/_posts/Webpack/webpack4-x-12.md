---
title: 从零开始webpack4.x（十二）多页面应用-抽离公共代码
date: 2020-04-12 17:16
tags: webpack
---

## 多页面应用-抽离公共模块

``` js
module.exports = {
    optimization: {
        splitChunks: {
            // 分割代码块（多页应用才会用到）
            cacheGroups: {
                //缓存组
                common: {
                    // 公共的模块
                    chunks: "initial", // 从开始处抽离，有多种配置，像异步模块什么的
                    minSize: 0, // 最小大小
                    minChunks: 2 //  引用次数
                },
                vendor: { // 此处为了抽离第三方的公共模块，比如jquery（前提是index和other都引入jquery了）
                    priority: 1, //权重， 如果不给这个字段，那么就此例来说，会先走上边的“common”，会把jquery和a.js，b.js合并在一个文件中。
                    //如果还有别的入口只使用jquery了，但是a和b对于它来说就是无用的。加上权重之后，会将第三方模块单独抽离
                    test: /node_modules/,
                    minSize: 0, // 最小大小
                    chunks: "initial",
                    minChunks: 2 //  引用次数
                }
            }
        }
    }
}
```
