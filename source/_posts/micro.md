---
title: micro
date: 2022-01-26 16:15:24
categories: micro
tags: [micro]
comments: true
---


# 执行多个命令

## npm-run-all插件
- [官网地址](https://www.npmjs.com/package/npm-run-all):介绍很简短，这种小插件不如自己写一个。
- linux系统、shell脚本

## node环境执行shell命令
- package.json下的script脚本，可以执行shell命令，可以通过不停的`cd npm install && cd.. `
```json
"script": {
    "start": "cd main && npm install && cd.. && cd vue-sub && npm install"
}
```

- 通过`child_process.exec`执行shell命令
```javascript
const childprocess = require('child_process')
childprocess.spawn('cd mian && npm run serve',{stdio:"inherit",shell:true})

```
