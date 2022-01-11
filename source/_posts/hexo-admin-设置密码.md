title: hexo-admin 设置密码
author: Laiyong Wang
date: 2022-01-05 15:52:41
tags:
---
修改站点配置文件_config.yml:
```bash
#admin
admin:
  username: ****
  password_hash: $2a$10$uRPOBm1h/tikf9Zc98RY8.MRX0LKbyHCOnrrzaSwz6Ah9nbSWbPPK
  secret: *****
```
username是用户名
password_hash是密码的哈希映射值，由于不同版本的node.js的哈希算法是不一样的，所有用以下方法来产生有效的密码哈希值
```bash
$ node
> const bcrypt = require('bcrypt-nodejs')
> bcrypt.hashSync('secret')
//'$2a$10$uRPOBm1h/tikf9Zc98RY8.MRX0LKbyHCOnrrzaSwz6Ah9nbSWbPPK'
>
```