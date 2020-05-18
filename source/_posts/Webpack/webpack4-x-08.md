---
title: 从零开始webpack4.x（八）source-map
date: 2020-04-08 17:38
tags: webpack
---

## source-map

新建一个配置
在mode: 'production'下 报错提示
home.js

``` js
console.log('index');

class Log {
    constructor() {
        console.lo(123); // 错误位置
    }
}

let log = new Log();
```

没有使用devtool时，浏览器错误提示截图 打包压缩后的文件 不利于查找错误

![console](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408171755273-659566171.png)

![source](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408171816177-1910129741.png)

1. 使用source-map提示截图

生产单独映射文件 报错提示 报错代码查看

![终端](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408172044194-441465431.png)

![console](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408172101401-807003833.png)

![source](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408172119718-1454925395.png)

2. 使用eval-source-map提示截图

不会产生单独文件

![console](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408172421913-49020326.png)

![source](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408173923383-1942747281.png)

3. 使用cheap-module-source-map截图

产生单独文件 不显示列

![终端](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408172650333-1772955247.png)

![console](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408174046709-825282248.png)

![source](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408174007271-821745874.png)

4. 使用cheap-module-eval-source-map

不产生单独文件和列  集成打包后文件 警告 home.js超过244KiB 可能会影响web性能。

![终端](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408173125304-1786836063.png)

![console](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408173313517-1885344390.png)

![source](https://img2020.cnblogs.com/blog/1153103/202004/1153103-20200408173808334-1160595125.png)

 webpack.config.js

``` js
let path = require('path');
let HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    mode: 'production',
    entry: {
        home: './src/home.js'
    },
    output: {
        filename: '[name].js',
        path: path.resolve(__dirname, 'dist')
    },
    // 1) 源码映射 会生成一个单独的sourcemap文件 出错了 会标识 当前报错的列 行 1.生产文件大 全面
    // devtool: 'source-map',
    // 2）不会产生单独文件 但可以显示列 行
    // devtool: 'eval-source-map',
    // 3）不会产生列 有单独映射文件
    // devtool: 'cheap-module-source-map',
    // 4）不会产生文件和列 集成打包后的文件中 
    // devtool: 'cheap-module-eval-source-map',
    plugins: [
        new HtmlWebpackPlugin({
            template: './index.html',
            filename: 'index.html'
        })
    ],
    module: {
        rules: [{
            test: /\.js$/,
            use: {
                loader: 'babel-loader',
                options: {
                    presets: [
                        '@babel/preset-env'
                    ]
                }
            }
        }]
    }

}
```
