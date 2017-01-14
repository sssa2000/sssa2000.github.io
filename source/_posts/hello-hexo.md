---
title: hello hexo
date: 2017-01-15 02:20:56
tags: 杂
---


![](hello-hexo/1.jpg)

## 起因

重新部署octopress的时候又遇到各种问题，
首先是我发现octopress出到3了，于是很自然的就不能用旧版本了，
按照octopress3的文档安装部署总是不能顺利的完成，
而且看了一下octopress3的源码提交，好像也没有人继续维护了的感觉，
在octopress的官网上也没有把3作为一个正式的产品推出，所以还是有点心生退意。

恰好之前看到说Hexo用js编写的，用起来也方便，于是就试试。
易用性方面Hexo确实是足够简单了。
很多人看不上Hexo的原因居然是不够复杂，不够酷。这一点其实我不太敢赞同。因为我觉得工程师也好Hacker也好程序员也好，都不会为了过瘾去做某些事情。 大家在Github上部署静态blog的原因无非就是：
- 免费
- 自由
- 完全可控

并不是为了炫耀，为了显得与众不同，为了这样的目的去做任何事情其实都有点幼稚的感觉。

持续写blog的目的其实就是为了写作，记录，整理自己的所得。
我选择静态blog 而不是wordpress 来编写tech类的blog的原因其实主要是因为在格式控制、渲染公式、图片、表格上 更可控。

------

## 初次安装
我并没有选择迁移octopress的数据，而是直接把github的repo清空了，重新开始的。

遇到的仅有的两个问题
- git clone 以后，如果执行 hexo init，hexo会把repo的git信息全部清理掉。所以，我采取的方式是，在其他目录hexo init，然后把hexo拷贝到repo下。
- config.yml中冒号后面必须有空格的问题，这个是一个大家都知道的bug，不再多言。

整个过程是这样的：
- 安装node.js
- 安装Hexo：	`sudo npm install -g hexo`
- 建立一个目录，用来init hexo。
```
$ mkdir hexo 
$ cd hexo 
$ hexo init
```

- 修改_config.yml:
```
title: Hippo & YirigaCheffee
subtitle: sssa2000's tech blog
description: 
author: sssa2000
language: zh-CN
url: http://sssa2000.github.io
post_asset_folder: true
deploy:
  type: git
  repo: https://github.com/sssa2000/sssa2000.github.io.git
  branch: master
```

- 到另一个目录blog，clone github的repo：
```
git clone https://github.com/sssa2000/sssa2000.github.io
```
- 创建分支hexo_source，并且切换到这个分支。这个分支是用来把hexo的设置、主题、以及原始的md文件同步到github上的。当然其实也可以不同步到github，保存到网盘或者单独起一个repo也可以。
```
git branch hexo_source
git checkout hexo_source
```
- 把hexo目录下的文件，拷贝到repo目录下。
- 在repo目录下 执行npm安装几个hexo的插件
```
$ npm install hexo-deployer-git --save
$ npm install hexo-renderer-jade --save 
$ npm install hexo-renderer-sass --save
$ npm install https://github.com/CodeFalling/hexo-asset-image --save
```
- 在repo的目录下执行生成预览命令，hexo clean很重要！
```
$ hexo clean 
$ hexo generate 
$ hexo server  
用 http://localhost:4000/ 在本地预览
```
- 用hexo d 来发布网站。
- 用以下命令把网站源文件备份
```
确保切换到hexo_source分支
git checkout hexo_source
git add .
git commit -m "save blog"
git push origin hexo_source
```

------
##在其他机器安装