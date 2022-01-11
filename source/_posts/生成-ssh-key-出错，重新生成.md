title: 生成 ssh-key 出错，重新生成
author: Laiyong Wang
date: 2022-01-11 10:42:11
tags:
---
#### 报错内容
No such file or directory

Could not open a connection to your authentication agent

#### 操作步骤
##### 检查已有的SSH keys

```
ls -al ~/.ssh
```
##### 生成新的SSH key

```
ssh-keygen -t rsa -C "youremail@example.com"
```
然后将这个新的key添加到ssh-agent中：

```
ssh-agent -s
ssh-add ~/.ssh/id_rsa
```
如果ssh-add出错，执行ssh-agent bash，再次回到ssh-add命令
##### 将SSH key添加到你的GitHub账户
在github的账户页的右上角，点击Setting—>SSH keys—>Add SSH key，在"title"栏输入一个自己喜欢的标题，“key”栏中粘贴id_rsa.pub的公钥内容，最后点击“Add key”按钮
##### 检查SSH key是否成功设置
```
ssh -T git@github.com
```
如果得到下面的结果，说明你的设置是成功的！
Hi username! You’ve successfully authenticated, but GitHub does not

参考网站：https://blog.csdn.net/qq_43634735/article/details/121197352?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&utm_relevant_index=2