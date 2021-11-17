---
title: typescript + jquery
date: 2021-11-17 14:30:24
categories: typescript
tags: [typescript, jqeury, webpack]
comments: false
---、


> 当只做几个单页面的时候，并不需要用到其他框架，可以考虑ts+jquery+webpack简单方便,

# 版本
- node -v `v10.15.3`
- npm -v  `7.20.6`
- tsc -v  `4.4.3` <font color="red">【若没有，请全局安装`npm install typescript -g`】</font>
- webpack -v  `5.52.1`
- webpack-cli -v `3.3.12` <font color="red">【webpack-cli下载版本过高，会导致版本不兼容等】</font>
- webpack-dev-server -v  `3.11.2`

<!-- more -->

# 结构
> 跟其他项目一样有个<font color="red">【src】</font>，执行打包命令行后，会有个dist文件夹，里面才是上线的代码
- `src/asset`: 存放第三方库eg:**jqeury**等，webpack不会做打包处理，直接原封copy
- `src/css` : 样式文件夹
- `src/images` : 图片文件夹
- `src/js` : typescript转移后生成的js
- `src/ts` : ts文件
- `src/index.html` : 跟页面<font color="red">【需要手动引入js等文件】</font>

# npm初始化
- 新建目录proect并在当前目录执行`npm init` 一路回车，会在当前目录下生成package.json
- 修改package.json文件，找到`scripts`对象，<font color="red">莫急、等webpack配置完才可运行</font>
```javascript

```
- 下载插件`npm install cross-env -S` 
> cross-env作用：当我们在打包的时候需要区分**测试环境**、**生产环境**，不同环境打包配置不一样，cross-env可以自定义变量传递到node.js环境中。[官方解释](https://www.npmjs.com/package/cross-env)
> 如下命令：`cross-env NODE_ENV=sit` 相当于在<font color="red">node.js</font>中执行了如下代码
```javascript
// 切记node.js环境
process.env.NODE_ENV = 'sit'
// 所以可以通过 process.env对象访问
```

# typescript初始化
- 执行`tsc --init` 会生成tsconfig.json配置文件
- 修改配置文件，我的配置文件如下
> 如下配置执行`tsc`时，会自动检索`include`配置中的ts文件，输出到`outDir`配置js文件中，并转义成es5
```javascript
{
  "compilerOptions": {
    "removeComments": true, /*不输出注释*/
    "sourceMap": false,/*生成目标文件 inline sourceMap*/
    "declaration": false,/*生成响应的 d.ts文件*/
    "strict": true,/*启用所有严格类型检查选项*/
    "strictFunctionTypes": false,/*不润徐函数参数双向协变*/
    "moduleResolution": "node",/*指定模块解析器*/
    "target": "ES5",/**/
    "outDir": "./src/js",/*指定文件的输出目录*/
    "lib":[
      "ES5",
      "ESNext",
      "DOM",
      "DOM.Iterable",
      "ScriptHost",
    ]
  },
  "include": ["./src/ts/*"]/*编译器需要编译的文件或目录，支持通配符*/
}
```

# 配置webpack
- 安装依赖`npm install webpack@5 webpack-cli@3 webpack-dev-server@3 -D`
- 安装依赖`npm install copy-webpack-plugin -D`
- 当前页面新建文件`webpack.config.js`  
> `mode`模式可根据大家爱好自行修改，当然模式不一样，打包后的代码也不一样。
```javascript


```

**1、配置文件中用到了node.js模块`path`、`fs`、`process`**
- [path](http://nodejs.cn/api/path.html): node.js环境中因操作系统不同，文件路径也有有所差异eg:`\`、`/`,为了统一化并快速获取根目录，在node中才有了`__dirname`这个全局变量，可以返回当前文件的**绝对路径**。
> 切记`__dirname`是node中的全局变量，如何验证？ 可以创建个js文件，
```javascript
// index.js
console.log('__dirname', __dirname);
```
> 终端在此目录执行`node index.js`命令， 会发现输出当前磁盘**根目录**  <font color="green">【因为这是在node环境执行的脚本】</font>
> 你若新建index.html引入index.js文件的时候，会发现控制台输出 `undefined`, 因为浏览器环境根本没有这个变量。
- [fs](http://nodejs.cn/api/fs.html): fs.readdirSync(dirPath)读取`src`目录下的文件， 并返回数组形式的文件名，eg:['assets', 'css', 'js]等。
- [process](http://nodejs.cn/api/process.html#processenv):对象提供有关当前 Node.js 进程的信息、用户环境信息等，如：node安装目录等。

**2、配置文件中用到了webpack插件`copy-webapck-plugin`**
- [copy-webapck-plugin](https://www.npmjs.com/package/copy-webpack-plugin):简单暴力只需要按照文档配置入口出口，就可以将文件copy一份到出口中。
> 可以将一些不需要处理的文件（图片、jquery、等）直接copy一份
- webpack自带属性`DefinePlugin`：可以将node.js环境中的变量托管到**js**代码中实现互通
> 打包的时候会自动赋值
```javascript
// webpack.config.js 文件
new webpack.definePlugin({
    'process.env.actCodeUUID':JSON.stringify(actCodeUUID)
})
// index.js文件访问
console.log(process) // undefined
console.log(process.env.actCodeUUID) // 有值

```