---
title: 从零开始webpack4.x（一）介绍
date: 2020-03-12 20:37
tags: webpack
---

# Webpack
本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

![bundler](https://img2020.cnblogs.com/i-beta/1153103/202003/1153103-20200312194629159-106552335.png)

### 一、Webpack的功能有那些
#### 代码转换
ES6转ES5、less，sass转css

#### 文件优化
压缩代码体积、合并文件

#### 代码分割
多页面应用-公共模块抽离、路由懒加载

#### 模块合并
多模块合并成一个

#### 自动刷新
热更新

#### 代码校验
eslint、tslint

#### 自动发布
打包并发布到服务器

### 二、Webpack前置技能
node基础、npm相关命令、ES6语法

### 三、Webpack最终需要掌握内容
webpack常见配置、webpack高级配置、webpack优化策略、ast抽象语法树、webpack中的Tapable、手写webpack、手写webpack常见的loader、手写webpack常见的plugin

