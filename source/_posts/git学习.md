---
title: git学习
date: 2020-10-22 18:08:07
tags:
categories: git
---

今天找到一个非常直观的git学习网站 https://learngitbranching.js.org/  为啥需要学习git呢，本来以为自己已经学会了，结果发现在某种使用场景中，自己又找不到对策了，很多概念都弄不懂，感觉连“知其然”都做不到，更不要说“所以然”了。  

Let's go!  
<!--more-->




## __Appendix__  

### __git merge : 3-way merge algorithm__
[How does 'git merge' work in details?](https://stackoverflow.com/questions/14961255/how-does-git-merge-work-in-details)  
[Merge (version control)](https://en.wikipedia.org/wiki/Merge_(version_control))  
[Strategies and Tools for Resolving Git Merge Conflicts](https://www.savaslabs.com/blog/strategies-and-tools-resolving-git-merge-conflicts#:~:text=In%20a%20three%20way%20merge,before%20the%20two%20branches%20forked)  


### __git rebase: 变基的原理是什么__  

### __git reset: reset之后某些丢掉的commit能被找回来吗__  
[How can I recover a lost commit in Git?](https://stackoverflow.com/questions/10099258/how-can-i-recover-a-lost-commit-in-git)  

### __git reset: reset如何调整commit的顺序？__  
rebase调整commit的原理是什么？
### __git cherry-pick__  

1. cherry-pick 多个不同branch的commit，可以直接像下面这么写吗 git cherry-pick commit1 commit4 commit9（这三个commit分属不同的branch）  
2. cherry-pick的commit是由merge而来的，此时会出现什么问题？遇到这种情况，该怎么处理？  


### __如何定位哪个commit开始引入了某个bug__  


### __之前的某个commit存在bug，该如何修复__ 
* 直接新加一个修复bug的commit，简单直接  
* 
