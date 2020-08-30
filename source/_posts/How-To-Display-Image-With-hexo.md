---
title: How To Display Image With hexo
date: 2020-08-30 17:55:46
tags:
categories: other
---

<font color=0xFFFFFF>本文所有方式均通过亲自验证</font>

刻舟求剑是什么意思，今天算是认识到了。  
为什么信息越来越多，我们却越来越累了呢，是因为有其他的东西吸引了我们本来的注意力吗？有这种原因，但还有更重要的是，有效信息太少。  
今天我在网上找hexo显示本地图片的方式，尝试了很多都有问题，搜索出来的结果绝大部分都是无效信息。  
下面我们说一说这些无效信息。  

__npm install hexo-asset-image__  
安装这个插件之后，显示图片不对。通过查看public目录下生成的index，发现是生成的图片link不对。  
我们用上面这个命令安装的是hexo-asset-image 1.0.0版本，这个版本跟hexo3兼容上有问题，无法正确生成图片link。  

__npm install https://github.com/CodeFalling/hexo-asset-image --save__  
有的blog发现了这个问题，提示要用这个版本的插件0.0.5。  
但这个插件也有问题，我们需要修改hexo-asset-image的index.js文件，可以参考[hexo 图片路径错误/.com//](https://segmentfault.com/q/1010000020310187/a-1020000020311907)  
也就是说，用很老的版本是可以的，但同样的，这个老版本的插件跟hexo3及以上版本也存在兼容问题。  

上面是网络搜索的两个主要结果，其实主要结果是第1个，第2个我也是在segmentfault上找到的。  

还好，搜索引擎给了我一个更好的答案。  

__hexo原生支持image__  

[关于hexo博客图片插件问题](https://www.jianshu.com/p/7f06d10f2e3e)  

[hexo文档：资源文件夹](https://hexo.io/zh-cn/docs/asset-folders)  

根据官方文档的说明，目前版本的hexo可以直接显示图片，主要有两种方式

1. source/images文件夹下面放置图片，通过\!\[](/images/sample.jpg)直接显示
2. 通过文章资源文件夹   
   1) _config.yml中　post_asset_folder : true
   2) hexo n title "name" 会生成同名的md和文件夹  
   3) 把图片资源放置到对应的文件夹，这时候有两种方式来显示图片  
      markdown方式： \!\[](1.png) 这种方式会导致的结果是，在首页中看不到图片，但如果点开具体文章，图片是可以正常显示的。    
      正确方式：hexo3支持的标签用法 {% asset_img example.jpg This is an example image %}  


所以，原来的很多具体的处理措施，现在并不可用了，但方法该是我们掌握的，比如这里遇到无法显示的问题，首先要排查生成的html里image link是否正确，然后最好的方式是寻找官方文档的支持，说实话，现在搜索引擎的权重设计的并不好，很多已经无法用的方案，还在置顶，不知道因为作者是做了SEO优化，还是说搜索引擎本身并不智能到判断这些信息已经是不合适的了。  

缘木求鱼，刻舟求剑。从古至今，大的道理还是那么多，关键是如何变成自己的模型。  
