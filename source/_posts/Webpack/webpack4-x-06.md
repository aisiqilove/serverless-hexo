---
title: 从零开始webpack4.x（六）全局变量引入
date:  2020-03-13 01:16
tags: webpack
---

## 以jQuery的$为例

index.js

``` js
// import $ from 'jquery';
// import $ from 'expose-loader?$!jquery'; 内联写法
// expose-loader 暴露 全局的loader normal 普通loader 内联loader 后置 postloader
// console.log(window.$);
console.log($);

// 三种方法
//  1. 在配置modul>rules中 expose-loader 暴露到window
// {
//     test:require.resolve('jquery'),
//     use: 'expose-loader?$'
// },
//  2. 在配置plugins 中添加new webpack.ProvidePlugin({ $: 'jquery' }) 给每个模块都注入一个$
//  3. cdn外部引入不打包 index.html引入外部jquery webpack配置文件中加上 externals: { jquery: jQuery }
```
