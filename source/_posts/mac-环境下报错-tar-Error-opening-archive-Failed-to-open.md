title: 'mac 环境下报错 tar: Error opening archive: Failed to open'
author: Laiyong Wang
date: 2022-01-05 15:22:54
tags:
---
#### 临时修改去掉国内的镜像设置: 在 Terminal 中输入下面的命令即可
```bash
export HOMEBREW_BOTTLE_DOMAIN=''
```

#### 通过更新profile文件永久修改设置
zsh是\~/.zprofile文件，bash要修改~/.bash_profile文件. 如果你并不知道这俩是什么东西, 推荐使用方案1.