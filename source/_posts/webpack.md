---
title: webpack
date: 2021-12-20 16:15:24
categories: webpack
tags: [webpack]
comments: true
---

----
> webpack是架构之路的绊脚石, webpack5跟webpack4比做了很大的提升，既要了解不同之处又要区分如何使用。

> 目前各种框架(vue-cli2、vue-cli3、react等)又**集成**`webpack`,导致webpack配置乱套，很是`反感`，估计这篇文章会很长请耐心开完。

`基础配置`就不讲解了，说一些自己学习中碰到的`坑`！

----

1. 文件中导出了一个函数 https://webpack.docschina.org/configuration/output/#outputlibrary

# webpack环境搭建
## webpack全局安装
- `npm install webpack -g`
- 当我们执行`webpack --config {文件名}` 命令的时候，具体做了哪些？
    1. 寻找`node_modules`文件下的`.bin`文件
    2. `.bin`在指向真实的配置文件包
    3. 配置文件会作为webpack(config)：`config`参数对象传递给webpack;
    ```javascript
    // webpack.config.js 文件
    // 会将module.exports暴露的模块传递给webpack
    const path = require('path')
    module.exports = {
        mode: 'none',
        entry: {
            main: './src/a.js',
        },
        output: {
            filename: 'a.[chunkhash].js',
            path: path.join(__dirname, './dist'),
        }
    }
    ```
2. node命令执行`node {文件名字}`,局部安装webpack并引入执行配置对象即可
- 这个就很容易理解，直接让node执行配置文件即可，[如何开启webpack配置文件](https://webpack.docschina.org/api/node/) 
```javascript
// node.js文件
const path = require('path')
const webpack = require('webpack')
webpack({
    mode: 'none',
    entry: {
        main: './src/a.js',
    },
    output: {
        filename: 'a.[chunkhash].js',
        path: path.join(__dirname, './dist'),
    }
},(err) => {
    console.log(err)
})
```

----

<!-- more -->

## 切换webpack版本
- 现在`webpack5`已出，但是好多公司都在用`webpack3`俩者都要兼顾会使用。 
- 我全局安装的是webpack5, 要如何切换webpack3进行学习呢？
> 查看当前环境版本 `webpack -v`
```javascript
webpack: 5.66.0
webpack-cli: 4.9.1
webpack-dev-server 3.11.2
```
> 当前文件夹下`package.json`
```json
"devDependencies": {
"webpack": "^3.12.0"
}
```
> 配置文件webpack.conig.js
```javascript
// webpack.conig.js文件
const path = require("path");
const webpack = require("webpack");
const packagejson = require("./package.json");

const config = {
    entry: {
        first: './src/first.js',
        second: './src/second.js',
        vendor: Object.keys(packagejson.dependencies)//获取生产环境依赖的库
    },
    output: {
        path: path.resolve(__dirname,'./dist'),
        filename: '[name].js'
    },
    plugins: [
        new webpack.optimize.CommonsChunkPlugin({
            name: 'vendor',
            filename: '[name].js' 
        }),
    ]
}
module.exports = config;
```
- 碰到的问题， 在当前文件夹下`终端`执行 `webpack --config weback.conig.js` 一直报错
```
TypeError: compiler.plugin is not a function
    at CommonsChunkPlugin.apply (/Users/even/Even/continue/packagingTools/webpack-study/webpack-CommonsChunkPlugin/CommonsChunkPlugin01/node_modules/webpack/lib/optimize/CommonsChunkPlugin.js:88:12)
```
> 仔细看第二行 指向了webpack5全局的包，所以报错高版本并不兼容3，切换全局webpack成本有点大。
- 突然想到如果在`package.json`里面执行命令，是否会指向当前文件的webpack3版本呢？ 果然真的成功了。。。。。
```json
"scripts": {
    "build": "webpack --config webpack.config.js"
},
```
----

# webpack常规配置
## mode的作用
- [官网的解释](https://webpack.docschina.org/configuration/mode/)就不在重复了. 直接看生成的代码[gitlab代码]()
> 要打包的代码
```javascript
// a.js
console.log('我是a模块')
function a() {return '111'}
a();
```
> production
```javascript
console.log("我是a模块"),window.even={};
```
- 可以看出代码几乎没怎么变，出来抛出变量之外

> development
```javascript
(() => { 
var __webpack_modules__ = ({
"./src/a.js": (() => {
eval("console.log('我是a模块')\nfunction a() {return '111'}\na();\n\n//# sourceURL=webpack://even/./src/a.js?");})
});
var __webpack_exports__ = {};
__webpack_modules__["./src/a.js"]();
window.even = __webpack_exports__;
})();
```
- 开发模式下代码完全不一样了，把我们的代码变成字符串了，这又是为什么呢，
- 其实是为了方便我们代码的热更新，以及模块化引入。 大家如果对eval不理解可以去看看我的上一篇[文章]()


## externals
- [externals](https://webpack.docschina.org/configuration/externals/):防止将某些 import 的包(package)打包到 bundle 中，而是在运行时(runtime)再去从外部获取这些扩展依赖(external dependencies)。


## library、libraryTarget作用
> [参考文章](https://zhuanlan.zhihu.com/p/108216236)
> [官网解释](https://webpack.docschina.org/configuration/mode/)
> [个人github代码]() 不多说直接上图
- library : 全局使用的名称变量
- libraryTarget : 暴露方式，是commonjs、commonjs2、umd还是this、var等
![](/images/library.jpg)


## 多个webpack配置合并
- 当使用`webpack-merge`合并代码时,第二个参数会覆盖第一个。
```javascript
// webpack-base.js
const path = require('path')
module.exports = {
    entry: {
        main: './src/a.js'
    },
    output: {
        filename: 'a.[chunckhash].js',
        path: path.join(__dirname, './dist')
    }
}
// webpack01.js
const path = require('path');
const webpack = require('webpack');
const {merge} = require('webpack-merge');
const base = require('./webpack-base.js')
let webpackConfig = merge(base,{
    output: {
        filename: 'a.[chunckhash].js',
        path: path.join(__dirname, './dist2')
    }
})
webpack(webpackConfig,(err) => {
    console.log(err);
})
```
> 执行命令 node node.js


## 设置环境变量
### cross-env 插件
- 以下命令会将环境变量`APP_ID`的值 插入`process.env`对象中
```javascript
// 下载插件
npm install cross-env -S
// 使用 在命令行中
cross-env APP_ID=111111 node ./test.js
```

### npm run {命令} --APP_ID=111111
- 也可以通过npm方式插入环境变量-执行命令`npm run dev --APP_ID=111111`
```json
// package.json文件下的
"scripts": {
    "dev": "node ./test.js",
}
```
> `--`:代表前缀`npm_config_`
> `APP_ID`转移成小写，最后会看到属性名为 `npm_config_app_id`,值为`11111`
> 会将环境变量插入`process.env`对象中
- 当然也可以放多个参数如 `npm run dev --APP_ID=111111 --APP=22222`等

## 如何删除文件
### rimraf 
- [npm包](https://www.npmjs.com/package/rimraf)`rimraf`
```javascript
// node包 代码
const rm = require('rimraf');
const path = require('path')
rm(path.join(__dirname,'./dist/index01.js'),function(err) {
    if (err) throw err;
})
```
### clean-webpack-plugin 
- [webpack插件](https://www.npmjs.com/package/clean-webpack-plugin)`clean-webpack-plugin`
```javascript
// webpack插件代码
const {CleanWebpackPlugin} = require('clean-webpack-plugin');
plugins: [
    new CleanWebpackPlugin(),
]
```
> 俩者差距还是很大的，一个是node包好处是可以删除任意文件任意目录。一个是webpack插件只能删除打包的路径并全量删除。

----

# webpack优化

## CommonsChunkPlugin
- [文章中大佬写的和很详细](https://segmentfault.com/a/1190000012828879),他是关于webapck3的相关配置；
- [分离出第三方库、自定义公共模块、webpack运行文件]()
- [单独分离出第三方库、自定义公共模块、webpack运行文件]()
- [抽离第三方库和自定义公共模块]()
- []()


## 多个入口文件合并
1. 在a.js 引入b.js, webpack打包的时候自动会合成
```javascript
// a.js
import a from './b.js'
// 配置文件只有a入口就可
let path = require('path')
module.exports = {
    mode: 'node',
    entry: './src/a.js',
    output: {
        filename: 'bunld.js',
        path: path.join(__dirname,'./dist'),
    }
}
```
2. 配置入口的时候多个入口
```javascript
let path = require('path')
module.exports = {
    mode: 'node',
    entry: {
        main: ['./src/a.js', './src/b.js']
    },
    output: {
        filename: 'bunld.js',
        path: path.join(__dirname,'./dist'),
    }
}
```

----

## 多个入口文件分别打包
- 其实分别打包文件就是将`entry`变成对象而已，是不是很简单

```javascript
let path = require('path')
module.exports = {
    mode: 'node',
    entry: {
        a: './src/a.js',
        b: './src/b.js',
    },
    output: {
        filename: '[name].[chunkhash].js',
        path: path.join(__dirname,'./dist')
    }
}
```

----

# webpack插件
## DefinePlugin - 定义环境变量
- [webpack.DefinePlugin](https://webpack.docschina.org/plugins/define-plugin#root):编译时将你代码中的变量替换为其他值或表达式
```javascript
// a.js文件
console.log('a文件')
let a = NAME;
// webpack.config.js
plugins: [
    new webpack.DefinePlugin({
        'NAME': 'Even',
    }),
]
// 打包好后的文件
console.log('a文件')
let a = Even;
```
- 会搜索打包文件变量`NAME`替换成`Even`变量。此时代码会报错，因为`let a = Even;`并没有定义。
- 如果想让`Even`是字符串请如下配置
```javascript
// webpack.config.js
plugins: [
    new webpack.DefinePlugin({
        'NAME': "'Even'",
    }),
]
```

## ExtractTextplugin - 文件提取
- [ExtractTextplugin](https://www.npmjs.com/package/extract-text-webpack-plugin)


# webpack解析器
## webpack-loader
- [loader](https://www.webpackjs.com/loaders/url-loader/)官网会发现loader大概有7个模块,**1、文件，2、JSON，3、转换编译，4、模板，5、样式，6、清理和测试，7、框架**
- webpack因为只识别`js`文件，所以需要这些loader来处理文件转成js,个个大神的开源代码和维护都在社区有明显标注

### vue-loader
- 细心的朋友会发现[vue-loader](https://vue-loader.vuejs.org/zh/)并不在上面[webpack-loader](https://www.webpackjs.com/loaders/url-loader/)里面, `why?`.
- **个人理解**: `.vue`是复杂的文件，文档loader都是处理单个文件,比如`.json`文件里面全是json数据，而`.vue`里面有三种**1.style,2.script,3.html**。
- 所以：为了把`vue`文件拆分成loader`（如：style-loader、html-loader、script-loader）`能够解析的文件,才有了`vue-loader`插件。[单独的配置文档](https://vue-loader.vuejs.org/zh/)
```javascript
// webpack.config.js
// 所有的vue项目，这个插件是必不可少的
const { VueLoaderPlugin } = require('vue-loader')
plugins: [
    new VueLoaderPlugin()
]
```
> 完全理解设计者的初衷，一个页面写所有的js、css、html真的很炫酷，不向其他开发者为了显示自己所谓的高端操作，`解耦后的代码`真的很难下咽,一个函数能搞定的事情非要给你弄三个文件，6个函数。。。

### url-loader
- 看到webpack代码,不是很理解`limit,name的作用`，于是去webpack官网找了很久。。。。才发现他是loader的配置。
```js
{
test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
loader: 'url-loader',
    options: {
        limit: 10000,
        name: path.join("../build", APP_ID, 'img/[name].[hash:7].[ext]')
    }
},
```
- limit:低于指定的限制时，可以返回一个 DataURL。
- name:这个参数让我很困惑,url-loader并没有对此字段做解释，查了(url-loader)[https://github.com/webpack-contrib/file-loader]之后才找到.