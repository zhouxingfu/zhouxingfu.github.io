---
title: All Elements in Two Binary Search Trees
date: 2020-09-24 17:42:32
tags:
categories: leetcode
---

__<font color=red>题目</font>__  

__Given two binary search trees root1 and root2.__

__Return a list containing all the integers from both trees sorted in ascending order.__  

__Example 1:__  

{% asset_img q2-e1.png %}  

    Input: root1 = [2,1,4], root2 = [1,0,3]
    Output: [0,1,1,2,3,4]

__Example 2:__  

    Input: root1 = [0,-10,10], root2 = [5,1,7,0,2]
    Output: [-10,0,0,1,2,5,7,10]  

__Example 3:__  

    Input: root1 = [], root2 = [5,1,7,0,2]
    Output: [0,1,2,5,7]  

__Example 4:__  

    Input: root1 = [0,-10,10], root2 = []
    Output: [-10,0,10]  

__Example 5:__  

{% asset_img q2-e5.png %}  

    Input: root1 = [1,null,8], root2 = [8,1]
    Output: [1,1,8,8]  


<!--more-->  

最直观的想法是先做遍历，然后得到两个有序的array，然后进行插入排序。  

这样的话整体的复杂度就是O(M+N)，比如要做两次，有办法优化吗？

