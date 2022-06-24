title: 提交项目至 github
author: Laiyong Wang
date: 2022-01-11 11:05:05
tags:
---
##### 为 GitHub 账号设置 SSH Key
参考：http://42.192.144.242/2022/01/11/%E7%94%9F%E6%88%90-ssh-key-%E5%87%BA%E9%94%99%EF%BC%8C%E9%87%8D%E6%96%B0%E7%94%9F%E6%88%90/
##### 提交代码到 Github
在配置完 GitHub 账号之后，我们便可以开始在上面存放项目代码了。首先 新建一个 GitHub 仓库，取名为 hello_laravel，填上 Description 项目描述，Initialize this repository with a README 这一项无需勾选，因为 Laravel 已默认帮我们创建好了 readme.md 文件

创建完成之后，使用以下命令将代码上传到 GitHub 上（将 your_username 替换为你自己的 GitHub 用户名）：

```
cd 项目目录
git remote add origin git@github.com:your_username/hexo.git
git push -u origin master
```