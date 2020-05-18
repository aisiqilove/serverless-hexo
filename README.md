# serverless-hexo
一个基于serverless和hexo的十分钟发布自己的博客

## 安装前提

* Node.js（Node.js 版本需不低于 8.6，建议使用 Node.js 10.0 及以上版本）
* Git

* 安装 Serverless Framework CLI

``` 
$ npm install -g serverless
```

* 安装 Hexo

``` 
$ npm install -g hexo-cli
```

安装 Hexo 完成后，执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

``` 
$ hexo init hexo   # 生成hexo目录
$ cd hexo
$ npm install
```

新建完成后，hexo 文件夹的目录如下：

``` 
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

可以通过 hexo g 命令生成静态页面

``` yaml
建议使用hexo clean 先清除生成静态页面
```

``` 
$ hexo g   # generate 

```

添加页面

``` 
$ hexo new page --path 路径/文件名 文件title
```

``` yaml
注：如果希望在本地查看效果，也可以运行下列命令，通过浏览器访问 localhost:4000 查看页面效果。
```

``` 
$ hexo s   # server
```

## 部署

``` 
$ sls --debug
```

## 移除

``` 
$ sls remove --debug
```
