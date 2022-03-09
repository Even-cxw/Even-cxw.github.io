---
title: 执行shell命令
date: 2022-01-27 16:15:24
categories: node
tags: [node]
comments: true
---


## child_process子进程
- 执行shell命令

```javascript
const childprocess = require('child_process')
childprocess.spawn('cd mian && npm run serve',{stdio:"inherit",shell:true})
```

- 删除非空目录

```javascript
const childprocess = require('child_process')
childprocess.spawn('rm -rf dist',{stdio:"inherit",shell:true})
```

## process获取当前目录路径
```javascript
// 获取当前文件所在目录
let path = process.cwd()
```
