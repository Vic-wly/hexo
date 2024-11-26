title: mac 使用 chrome 无视证书错误及禁用 CORS
author: Laiyong Wang
date: 2024-08-23 14:02:21
tags:
---
### 本地开发，项目需要 https 且前后端分离，前端通过 strict-origin-when-cross-origin 策略来接口，这样打开浏览器即可


```
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --disable-web-security --user-data-dir="/tmp/ChromeDevSession" --test-type --ignore-certificate-errors
```