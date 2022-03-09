---
title: vue
date: 2020-01-25 16:15:24
categories: vue
tags: [vue]
comments: true
---


# vue-cli3

## 吐槽！
- 本身webapck的配置就一大堆，又来了vue-cli3的配置，虽然做了简化，但是感觉完全没必要去学，只要能看懂就行
> vue-cli3已经出很久了，一直没有用最近在搞微前端，需要用vue-cli3里面的配置，突然一脸懵，真的是学不过来，哎！`简短记录下！`


## 配置文件
- webpack的位置文件是新建`webpack.config.js`
- vue的配置文件是新建`vue.config.js`
> 1. `publicPath`会将此参数，传递到`process.env.BASE_URL`
> 2. 创建 .env文件设置变量名`VUE_APP_{变量名}`， 也会传递到`process.env.VUE_APP_{变量名}`
```javascript
module.exports = {
  publicPath: '/subapp/sub-vue',
  transpileDependencies: ['common'],
  chainWebpack: config => config.resolve.symlinks(false),
  configureWebpack: {
    output: {
      // 把子应用打包成 umd 库格式
      library: `${name}-[name]`,
      libraryTarget: 'umd',
      jsonpFunction: `webpackJsonp_${name}`
    }
  },
  devServer: {
    port: process.env.VUE_APP_PORT,
    headers: {
      'Access-Control-Allow-Origin': '*'
    }
  }
}
```

## 添加element-ui
- 执行命令`vue add element`
- 有俩个选项 1.全局引入 2.按需引入
```
Fully import
Import on demand
```
- 我选择的是第二种按需引入，会`自动修改我们的配置文件`
```javascript
1. 多个plugins文件
2. bael.config.js文件修改
3. mian.js自动引入
```

<!--more-->