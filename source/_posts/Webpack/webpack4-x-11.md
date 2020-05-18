---
title: 从零开始webpack4.x（十一）webpack自带优化
date: 2020-04-12 17:06
tags: webpack
---

## webpack自带优化

1. tree shaking

2. scope hosting

test.js

``` js
let sum = () => {
    return 10;
}

let mmsum = () => {
    return 20;
}

export default {
    sum,
    mmsum
}
```

index.js

``` js
// import 在生成环境下 会自动去掉没用的代码
// 1）tree-shaking 打包时 把没用到到代码， 自动删掉
// es6 模块会把结果放到default上
let calc = require('./test')
calc.sum();

// 2）scope hosting 作用域提升 打包时也会把  webpack3.0后加上的优化
let a = 1;
let b = 2;
let c = 3;
let d = a + b + c; // 在webpack中自动省略 可以优化代码
console.log(d, "----------");
```
