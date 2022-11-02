---
layout: title
title: 已经被track的文件无法通过.gitignore文件生效
date: 2022-11-02 11:37:00
tags:
categories: [git]
---

最近遇到一个问题，把某些文件夹添加到 .gitignore，但git add的时候却还是添加进去了，不生效。

原因是，gitignore只针对untracked files。

如果是tracked files(之前已经commit过)，那么修改 .gitignore不能在 add的时候忽略掉tracked files。

__但如果我们的工作区还需要这些文件，那该怎么办？我们肯定是不希望在本地重新生成的，太浪费时间了。__

```xml
git rm -r --cached tempFile
git commit -m "从版本库移除 tempFile"
git push
```

当我们需要删除暂存区或分支上的文件，同时工作区**不需要**这个文件，可以使用 git rm

```xml
git rm file
git commit -m "delete file"
git push
```

当我们需要删除暂存区或分支上的文件，但是工作区**需要**这个文件，可以使用 git rm --cached