---
title: build and backup github pages with hexo
date: 2020-08-29 16:46:06
tags:
categories: other
---

之前用hexo搭建过blog，当时也新建了一个github repo用来备份，但不知道怎么恢复，因为是在虚拟机里搭的环境，后来也丢了，现在重新整理一遍，最重要的是备份，换台机器照样能搞定。  

## __<font color=0xFFFFFF>原理</font>__  
hexo依赖于node，环境是不需要备份的，那么我们要备份的是除了环境和包之外的东西。  
我们的github.io repo上有两个分支，其中master用来发布，hexo用来备份。 
注意：发布的东西和备份的东西是有差别的，备份的是源文件，发布的是hexo生成后的文件。  

我们的操作步骤是
1. 在github.io　hexo分支备份源文件
2. 本地文件夹blog负责发布master分支所需要的生成后文件

我们可以每次修改后，把blog里修改的文件拷贝到github.io/hexo下面，然后git push。但这样太过麻烦了，我们可以把blog中我们需要的文件git add，作为hexo分支所需要的文件，然后在同一个文件夹内，保持两个branch的内容。

下面我们梳理一下具体的流程。  

## __<font color=0xFFFFFF>node及hexo环境</font>__  

    mkdir blog
    npm i hexo-cli -g
    hexo -v 
    hexo init  
    npm install  

## __<font color=0xFFFFFF>github io</font>__  
在github上新建repo github.io，同时创建hexo分支，并把hexo分支选做default branch。  

## __<font color=0xFFFFFF>发布到github.io</font>__  
    npm i hexo-deployer-git

修改_config.yml文件

    deploy:
    type: 'git'
    repository: git@github.com:username/username.github.io.git
    branch: master
  
## __<font color=0xFFFFFF>hexo branch</font>__  

很奇怪，我们在hexo init的时候，确实是clone了themes，但在本地没有发现.git目录，只发现了.gitignore。  

所以，我们有两种处理方法
1. 把github.io 切换到hexo　branch，然后把.git目录拷贝到blog目录。此时，在blog目录里有了一个.gitignore，我们git add --all, git commit  git push origin hexo  
2. 把github.io　切换到hexo branch，把blog folder中需要的文件
```
    scaffolds/  
    source/  
    themes/  
    .gitignore  
    _config.yml  
    package.json  
```

拷贝到github.io所在的目录，然后  

    git add --all  
    git commit 
    git push --set--upstream origin hexo　
    　
然后把.git目录拷贝到blog folder

## __<font color=0xFFFFFF>发布文章</font>__  
经过上面的操作后，我们hexo g | hexo d就是发布到github.io/master，　我们git add | commit |  push就是发布到hexo  

## __<font color=0xFFFFFF>环境移植</font>__  
如果我们要在另一台环境上写我们的blog，那么我们  

~~mkdir blog_folder~~  
    ~~cd blog_folder~~  
    ~~npm install -g hexo-cli~~    
    ~~hexo init~~  
    ~~npm install~~  
    ~~git clone -b hexo git@github.com/username/username.github.io~~


The instrutions above is not right, you should do as follows

    git clone -b hexo git@github.com:zhouxingfu/zhouxingfu.github.io.git  blog_directory  
    npm install