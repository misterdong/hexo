title: Hexo+Git Page+Travis CI 搭建自动构建博客
date: 2018-10-31 16:49:00
tags: hexo
auther: dong
---
#### 前言
根据git page的特性，我们可以利用其特点利用[Hexo](https://hexo.io/zh-cn/index.html)做一个依据静态页面生成的博客，网上有很多类似的教程。同时，为了保存博客源码，以及创作的便利性，我们使用travis进行自动构建，免去了一些hexo的操作。
<!-- more -->
#### 创建github博客仓库 
##### 创建静态页面仓库
首先，在github上新建一个仓库，仓库名为：<用户名>.github.io,这个仓库用来存放博客的静态页面。
![](https://raw.githubusercontent.com/misterdong/img/master/blog0067tRJGly1fwrk9pz0esj30lo0gwtb4.jpg)
关于github page的发布方式有以下三种方式，
1. master分支直接发布
2. gh-pages分支发布
3. master分支下的/docs目录进行发布

任性的人以上三种都不采用，我选用的是再建一个仓库去存放hexo博客的源码，每次生成文件后同步到静态页面的仓库，
<用户名>.github.io->Settings->GitHub Pages,source默认选择master即可。

##### 源码仓库创建

Hexo前置操作
> 安装git
安装nodejs

具体安装方式请另行寻找，不在本章范围内。

安装完以上两个软件后，即可进行hexo的安装。

1. 安装hexo项目
`npm install -g hexo-cli`
2. 生成hexo项目
`hexo init [projectname]`
3. 上传hexo源码到源码仓库
`cd [projectname]`
`git add .`
`git commit -m "提交源码"`
`git push`

#### 使用Travis CI进行自动构建
##### 创建项目
登陆[Travis CI](https://travis-ci.org),登陆时选择用github账号进行登陆，这样可以自动同步所有项目。
然后到github的setting页面中完成[Access Token](https://github.com/settings/tokens)的申请，将其作为环境变量GH_TOKEN的值保存在环境参数里。
![](https://raw.githubusercontent.com/misterdong/img/master/blog0067tRJGly1fwrkadlb73j30k40ie408.jpg)
>关于github的token需要注意下，github有提示，token生成后显示一次，之后就再也看不到了。。。

##### 创建travis设置文件
让我们回到仓库本地目录，在该目录下新建一个.travis.yml文件，
文件内容如下：
``` yml
language: node_js
node_js: stable

# Travis-CI Caching
cache:
  directories:
    - node_modules


# S: Build Lifecycle
install:
  - npm install

before_script:
 # - npm install -g gulp


script:
  - hexo g

after_script:
  - cd ./public
  - git init
  - git config user.name "yourname"
  - git config user.email "your email"
  - git add .
  - git commit -m "Update blog"
  - git push --force "https://${GH_TOKEN}@${GH_REF}" master:master
# E: Build LifeCycle

branches:
  only:
    - master
env:
 global:
   - GH_REF: github.com/misterdong/misterdong.github.io.git
```
看到这里，我们就会发现travis其实就是一个帮你跑脚本的云主机命令行！
他按照我们指定的脚本顺序执行，先是install，再是before_script，script，最后是after_script.
branches指定了执行脚本的分支。
全局变量GH_REF指定了你的仓库地址。
我们首先用npm install在 travis 这台全新的主机上进行相关依赖的安装，这里就不需要重新再安装一遍 hexo 然后进行初始化了。安装完所需依赖之后，我们就可以直接使用 hexo 命令。
然后我们用hexo g命令来生成静态文件，生成的静态文件默认会被放在 public 目录下。
我们cd到public目录，用git init初始化仓库，进行相关信息的设置。
最后，travis-ci会将public目录下的文件全部 push 到你所创建的静态页面所在仓库的 master 分支上去。

当在hexo源码所在位置新写完文章后，用git方式进行提交，travis会发现文件变化，触发自动构建。
至此，我们完成了博客自动化构建的基本设置。

待续。。。。