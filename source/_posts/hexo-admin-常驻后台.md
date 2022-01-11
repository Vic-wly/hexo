title: hexo-admin 常驻后台
author: Laiyong Wang
date: 2022-01-05 15:49:19
tags:
---
原文链接：https://www.cnblogs.com/young233/p/14628456.html
### 安装 hexo-admin
```bash
//博客跟目录执行
npm install --save hexo-admin
```
### 安装pm2
```bash
npm install -g pm2
```

### 进到博客的根目录，新建一个文件：hexo_run.js
```bash
//run
const { exec } = require('child_process')
exec('hexo server',(error, stdout, stderr) => {
        if(error){
                console.log('exec error: ${error}')
                return
        }
        console.log('stdout: ${stdout}');
        console.log('stderr: ${stderr}');
})
```
启动这个进程服务
```bash
pm2 start hexo_run.js
```