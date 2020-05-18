# serverless-hexo
一个基于腾讯云serverless和hexo的十分钟发布自己的博客
使用主题是anatole-core基于anatole个性化修改

[博客地址](https://my-bucket-1254129026.cos-website.ap-guangzhou.myqcloud.com/)

``` yaml
更多问题查看 /themes/anatole-core/_config.yml
```

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
生成静态页面时，引用文件可能存在缓存
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

``` yaml
注：如您的账号未登陆或注册腾讯云，您可以直接通过微信扫描命令行中的二维码进行授权登陆和注册。
或者配置.env文件 
TENCENT_SECRET_ID=
TENCENT_SECRET_KEY=
```

## 部署

``` 
$ sls --debug
```

## 移除

``` 
$ sls remove --debug
```

[具体可参考完整模板仓库](https://github.com/tinafangkunding/serverless-hexo)
