---
title: node
date: 2022-01-27 16:15:24
categories: node
tags: [node]
comments: true
---

> 对常用的node如”path\process\module“等熟练使用，当涉及到新的领域，一脸懵，比如：最近想做`node环境中执行shell脚本`



# child_process
> 看着官网总感觉自己是外国人，完全看不懂，想罩着文档写点东西。`GG`；结束战斗，还是得靠大家的博客来学习，
## 如何在node环境执行shell脚本
```javascript
const childprocess = require('child_process')
childprocess.spawn('cd mian && npm run serve',{stdio:"inherit",shell:true})

```