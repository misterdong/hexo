---
title: hexo添加评论功能--gitalk
date: 2019-05-11 17:24:25
tags: hexo
---

昨天朋友想搭建博客，向他推荐了hexo自建博客，问到是否可以进行评论，发现我这里也没有加评论功能。
开始打算用gitment做评论，但是在安装完成后，在登录github时，遇到了问题，跨域请求无法访问，采用了第二种，即 gitalk。
<!-- more -->
#### gitalk


gitalk : 一个基于 Github Issue 和 Preact 开发的评论插件。[详情](https://github.com/gitalk/gitalk)

#### Register Application
github 上新建应用
![](https://raw.githubusercontent.com/misterdong/img/master/blog/20190511173939.png)
填写相关信息
![](https://raw.githubusercontent.com/misterdong/img/master/blog/20190511174046.png)
注册

![](https://raw.githubusercontent.com/misterdong/img/master/blog/20190511174156.png)
生成Client ID 和 Client Secret

#### gitalk.swig
新建/layout/_third-party/comments/gitalk.swig文件，并添加内容：

```script
{% if page.comments && theme.gitalk.enable %}
	<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
	<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
	<script src="/js/src/md5.min.js"></script>
	<script type="text/javascript">
    var gitalk = new Gitalk({
        clientID: '{{ theme.gitalk.ClientID }}',
        clientSecret: '{{ theme.gitalk.ClientSecret }}',
        repo: '{{ theme.gitalk.repo }}',
        owner: '{{ theme.gitalk.githubID }}',
        admin: ['{{ theme.gitalk.adminUser }}'],
        id: md5(tion.pathnam),
        distractionFreeMode: '{{ theme.gitalk.distractionFreeMode   }}'
    })
    gitalk.render('gitalk-container')
   </script>
{% endif %}
```
由于部分文章的评论区会报Error: Validation Failed,具体原因是由于 Github 限制 labal 长度不能超过 50引起的，该问题解决方案来自Gitalk项目仓 Issues115，通过MD5加密ID来缩短labal长度。
从GitHub下载md5.min.js文件，放置到themes\next\source\js\src\目录。[下载](
https://github.com/blueimp/JavaScript-MD5/blob/master/js/md5.min.js

#### comments.swig

修改/layout/_partials/comments.swig，添加内容如下，与前面的elseif同一级别上：
```script
{% elseif theme.gitalk.enable %}
<div id="gitalk-container"></div>
```
#### index.swig
引入gitalk.swig，修改layout/_third-party/comments/index.swig，在最后一行添加内容：
```script
{% include 'gitalk.swig' %}
```
#### gitalk.styl
新建/source/css/_common/components/third-party/gitalk.styl文件，添加内容：
```script
.gt-header a, .gt-comments a, .gt-popup a
border-bottom: none;
.gt-container .gt-popup .gt-action.is--active:before
top: 0.7em;
```
#### third-party.styl
修改/source/css/_common/components/third-party/third-party.styl，在最后一行上添加内容，引入样式：
```script
@import "gitalk";
```
#### _config.yml
在主题配置文件next/_config.yml中添加如下内容：
```script
gitalk:
  enable: true
  githubID: 你的用户id  
  repo: 你的仓库名   # 例：misterdong.github.io
  ClientID: 你的id
  ClientSecret:你的secret
  adminUser: misterdong #指定可初始化评论账户
  distractionFreeMode: true
```
至此，hexo添加gitalk评论功能完成，部分问题的解决方法，可参照
[https://liujunzhou.top/2018/8/10/gitalk-error/](https://liujunzhou.top/2018/8/10/gitalk-error/)


