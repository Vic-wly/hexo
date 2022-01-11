title: git 日常操作
author: Laiyong Wang
date: 2022-01-06 09:55:24
tags:
---
#### 强制远程覆盖本地代码
```bash
git fetch --all #取回远程库的所有修改；

git reset --hard origin/develop #指向远程库origin的develop（可更改成自己想要取的远程分支）

git pull #把远程库拉取到本地库
```

#### 将某一次或多次提交合并至某分支

```bash
git cherry-pick <commit id>:单独合并一个提交
git cherry-pick <commit id> <commit id>:合并多个提交,中间加空格即可
git cherry-pick -x <commit id>：同上，不同点：保留原提交者信息。
```
```bash
git cherry-pick <start-commit-id>..<end-commit-id>
git cherry-pick <start-commit-id>^..<end-commit-id>
```
前者表示把到之间(左开右闭，不包含start-commit-id)的提交cherry-pick到当前分支； 
后者有”^”标志的表示把到之间(闭区间，包含start-commit-id)的提交cherry-pick到当前分支。