---
title: webpack
date: 2021-12-20 16:15:24
categories: webpack
tags: [webpack]
comments: true
---

----

- 版本：
	`"webpack": "^4.31.0",`
	`"webpack-cli": "^3.3.2",`
	`"webpack-dev-server": "^3.3.1"`
----

# webpack环境搭建

## 配置⽂文件名称
- webpack 默认配置⽂文件:`webpack.config.js`
- 可以通过 `webpack --config 指定配置⽂文件`
- 当我们执行`webpack --config {文件名}` 命令的时候，具体做了哪些？
1. 寻找`node_modules`文件下的`.bin`文件
2. `.bin`在指向真实的配置文件包
3. 配置文件会作为webpack(config)：`config`（便是module.export暴露出的对象）参数对象传递给webpack;
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

## `node 执行webpack配置文件`
- [如何开启webpack配置文件](https://webpack.docschina.org/api/node/) 
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

## 通过npm srcript 运行 webpack

- 碰到的问题， 在当前文件夹下`终端`执行 `webpack --config weback.conig.js` 一直报错,报错片段如下！
```
TypeError: compiler.plugin is not a function
    at CommonsChunkPlugin.apply (/Users/even/Even/continue/packagingTools/webpack-study/webpack-CommonsChunkPlugin/CommonsChunkPlugin01/node_modules/webpack/lib/optimize/CommonsChunkPlugin.js:88:12)
```
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

> 仔细看第二行 指向了webpack5全局的包，所以报错高版本并不兼容3，切换全局webpack成本有点大。
- 突然想到如果在`package.json`里面执行命令，是否会指向当前文件的webpack3版本呢？ 果然真的成功了。。。。。
```json
"scripts": {
    "build": "webpack --config webpack.config.js"
},
```
----

## webpack文件监听
- 文件监听是在发现源码发⽣生变化时，⾃动重新构建出新的输出⽂文件。
- webpack 开启监听模式，有两种⽅方式: 唯⼀一缺陷:`每次需要⼿手动刷新浏览器器`
```javascript
//启动 webpack 命令时，带上 --watch 参数 
//在配置 webpack.config.js 中设置 watch: true
```
- ⽂文件监听的原理理分析
```javascript
//轮询判断⽂文件的最后编辑时间是否变化 某个⽂文件发⽣生了了变化，并不不会⽴立刻告诉监听者，⽽而是先缓存起来，等 aggregateTimeout
module.export = {
//默认 false，也就是不不开启
watch: true, //只有开启监听模式时，watchOptions才有意义 
	wathcOptions: {
		//默认为空，不监听的文件或者文件夹，支持正则匹配
		ignored: /node_modules/,
		//监听到变化发生后会等300ms再去执行，默认300ms
		aggregateTimeout: 300, //判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的，默认每秒问1000次 
		poll: 1000
	} 
}

```

## 文件指纹
- 文件指纹: 打包后输出的⽂文件名的后缀,文件指纹如何生成?
```javascript
// Hash:和整个项⽬目的构建相关，只要项⽬目⽂文件有修改，整个项⽬目构建的 hash 值就会更更改,
// Chunkhash:和 webpack 打包的 chunk 有关，不不同的 entry 会⽣生成不不同的 chunkhash 值
// Contenthash:根据⽂文件内容来定义 hash ，⽂文件内容不不变，则 contenthash 不不变
```

## 使用webpack内置的stats
- stats: 构建的统计信息
```javascript
// package.json 中使用 stats
"build:stats": "webpack --json > stats.json"
```

## 多进程/多实例构建
- 使用 HappyPack 解析资源

- 使用 thread-loader



# webpack常规配置

## Entry的用法
- 单入口：entry是一个字符串
```javascript
module.exports = {
	entry: './path/to/my/entry/file.js'
};
```
- 多入口：entry是一个对象
```javascript
module.exports = { 
	entry: {
		app: './src/app.js',
		adminApp: './src/adminApp.js'
	}
};
```

## Output 的⽤用法
- 单⼊入⼝口配置
```javascript
module.exports = {
	entry: './path/to/my/entry/file.js' 
	output: {
		filename: 'bundle.js'
		path: __dirname + '/dist' 
	}
};
```
- 多⼊入⼝口配置
```javascript
module.exports = { 
	entry: {
		app: './src/app.js',
		search: './src/search.js' },
		output: {
		filename: '[name].js', path: __dirname + '/dist'
	} 
};
```
## 核⼼心概念之 Loaders
- loaders的用法
```javascript
const path = require('path');
module.exports = { 
	output: {
	filename: 'bundle.js' },
	module: { 
		rules: [
			{ 
				test: /\.txt$/,   // test 指定匹配规则
			  use: 'raw-loader' // use 指定使⽤用的 loader 名称
			} 
		]
	} 
};
```

## 核⼼心概念之 Plugins
- 插件⽤用于 bundle ⽂文件的优化，资源管理理和环境变量量注⼊入
```javascript
const path = require('path');
module.exports = { 
	output: {filename: 'bundle.js' },
	plugins: [
		new HtmlWebpackPlugin({template: './src/index.html'}) // 放到 plugins 数组⾥里里
	] 
};

```


## 核⼼心概念之 Mode
- [Mode](https://webpack.docschina.org/configuration/mode/)⽤用来指定当前的构建环境是:production、development 还是 none

1. source map : 通过 source map 定位到源代码
> mode production 的情况下默认开启开发环境开启，
```javascript
// eval: 使⽤用eval包裹模块代码 
// source map: 产⽣生.map⽂文件 
//cheap: 不不包含列列信息
//nline: 将.map作为DataURI嵌⼊入，不不单独⽣生成.map⽂文件 
//module:包含loader的sourcemap
devtool: 'source-map'
```
2. tree shaking(摇树优化) : 只抽离bundle中有用的代码
> mode production 的情况下默认开启开发环境开启，

3. scope hoisting : 解决大量闭包代码
> mode production 的情况下默认开启开发环境开启，
> 原理理:将所有模块的代码按照引⽤用顺序放在⼀一个函数作⽤用域⾥里里，然后适当的重命名⼀一
些变量量以防⽌止变量量名冲突


## webpack打包库
> [参考文章](https://zhuanlan.zhihu.com/p/108216236) [官网解释](https://webpack.docschina.org/configuration/mode/)
- library : 指定的全局变量
- libraryTarget : 暴露方式，是commonjs、commonjs2、umd还是this、var等
![](/images/library.jpg)
- 如何指对 .min 压缩 `npm install terser-webpack-plugin@1.3.0`
```javascript 
const TerserPlugin = require('terser-webpack-plugin');
module.exports = {
    entry: {
        'large-number': './src/index.js',
        'large-number.min': './src/index.js'
    },
    output: {
        filename: '[name].js',
        library: 'largeNumber',
        libraryTarget: 'umd',
        libraryExport: 'default'
    },
    mode: 'none',
    optimization: {
        minimize: true,
        minimizer: [
            new TerserPlugin({
                include: /\.min\.js$/,
            })
        ]
    }
}
```

## 路径 [借鉴博客](https://zhuanlan.zhihu.com/p/36354511)

- 官网说：如果服务器下有文件需要设置webpack的publicPath配置，但又是为什么？
1. 我们先了解一下路径问题：
> 绝对路径指文件的完整路径，包括文件传输的协议HTTP、FTP等，一般用于网站的外部链接，
> 相对路径是指相对于当前文件的路径，它包含了从当前文件指向目的文件的路径，适用于网站的内部链接。
> 根路径的设置以“/”开头，后面紧跟文件路径，例如：/download/index.html。根路径必须在配置好的`服务器环境中`才能使用。
2. 如何使用根路径：
```javascript
<script src="/subVue/b.js"></script>
```
> 选择`路径`文件夹  利用http-server开服务  [代码]()
> 完全模拟生产环境服务器下的文件了，直接点击index.html就可以加载到`b.js`文件。
3. 修改webpack的publicPath其实就是修改了`script`引入路径变成根路径。代码如下
```javascript
// 当我们把publickPath设置成subVue时，html-webpack-plugin 插件自动替换根路径
<script src="/subVue/b.js"></script>
```


### output.path
- [官网地址](https://webpack.js.org/configuration/output/#outputpath): 参数`绝对路径`，将压缩好的代码打包到当前位置。

### output.publicPath
- 打包的配置
- [官网地址](https://www.webpackjs.com/guides/public-path/): 参数`相对路径`是`相对于build之后的index.html的`，打包后得到的html中可以看到。
- 项目中引用css，js，img等资源时候的一个基础路径 `静态资源最终访问路径 = output.publicPath + 资源loader或插件等配置路径`

### devServer.publicPath
- 在开发阶段，我们借用devServer启动一个开发服务器进行开发，这里也会配置一个publicPath，这里的publicPath路径下的打包文件可以在浏览器中访问。

### __webpack_public_path__ 
- [官网地址](https://webpack.js.org/api/module-variables/#importmetawebpackhot):决定了output.publicPath的值，用于来指定应用程序中所有的资源的基本路径.
- 这个变脸可以让我们在js代码中指定 output.publicPath变量；

## externals
- [externals](https://webpack.docschina.org/configuration/externals/):防止将某些 import 的包(package)打包到 bundle 中，而是在运行时(runtime)再去从外部获取这些扩展依赖(external dependencies)。


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
- 也可以通过npm方式插入环境变量-执行命令`npm run dev --APP_ID=111111` 切记： 一定是要在run后面跟参数；
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
### script命令删除
```javascript
rm -rf ./dist && webpack
```
> 俩者差距还是很大的，一个是node包好处是可以删除任意文件任意目录。一个是webpack插件只能删除打包的路径并全量删除。

----

# webpack优化

## CommonsChunkPlugin - 3之后已废弃
- [文章中大佬写的和很详细](https://segmentfault.com/a/1190000012828879),他是关于webapck3的相关配置；
- [分离出第三方库、自定义公共模块、webpack运行文件]()
- [单独分离出第三方库、自定义公共模块、webpack运行文件]()
- [抽离第三方库和自定义公共模块]()

## webpack5 - SplitChunks
- chunks选项，决定要提取那些模块。

> 默认是async：只提取异步加载的模块出来打包到一个文件中。
> 异步加载的模块：通过import('xxx')或require(['xxx'],() =>{})加载的模块。
> initial：提取同步加载和异步加载模块，如果xxx在项目中异步加载了，也同步加载了，那么xxx这个模块会被提取两次，分别打包到不同的文件中。
> 同步加载的模块：通过 import xxx或require('xxx')加载的模块。
> all：不管异步加载还是同步加载的模块都提取出来，打包到一个文件中。

- minSize选项：规定被提取的模块在压缩前的大小最小值，单位为字节，默认为30000，只有超过了30000字节才会被提取。
- maxSize选项：把提取出来的模块打包生成的文件大小不能超过maxSize值，如果超过了，要对其进行分割并打包生成新的文件。单位为字节，默认为0，表示不限制大小。
- minChunks选项：表示要被提取的模块最小被引用次数，引用次数超过或等于minChunks值，才能被提取。
- maxAsyncRequests选项：最大的按需(异步)加载次数，默认为 6。
- maxInitialRequests选项：打包后的入口文件加载时，还能同时加载js文件的数量（包括入口文件），默认为4。
> 先说一下优先级 maxInitialRequests / maxAsyncRequests <maxSize<minSize。
- automaticNameDelimiter选项：打包生成的js文件名的分割符，默认为~。
- name选项：打包生成js文件的名称。
- cacheGroups选项，核心重点，配置提取模块的方案。里面每一项代表一个提取模块的方案。下面是cacheGroups每项中特有的选项，其余选项和外面一致，若cacheGroups每项中有，就按配置的，没有就使用外面配置的。
- test选项：用来匹配要提取的模块的资源路径或名称。值是正则或函数。
- priority选项：方案的优先级，值越大表示提取模块时优先采用此方案。默认值为0。
- reuseExistingChunk选项：true/false。为true时，如果当前要提取的模块，在已经在打包生成的js文件中存在，则将重用该模块，而不是把当前要提取的模块打包生成新的js文件。
- enforce选项：true/false。为true时，忽略minSize，minChunks，maxAsyncRequests和maxInitialRequests外面选项

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

## css文件压缩
- npm install optimize-css-assets-webpack-plugin -D
- npm install cssnano -D
```javascript
module.exports = { 
	entry: {
		app: './src/app.js',
		search: './src/search.js' 
	},
	output: {
		filename: '[name][chunkhash:8].js', path: __dirname + '/dist'
	}, 
	plugins: [
		new OptimizeCSSAssetsPlugin({ assetNameRegExp: /\.css$/g, cssProcessor: require('cssnano’)
	]
```


## 自动清理构建目录
- [借鉴博客](https://juejin.cn/post/6844903853708541959) [html-webpack-plugin](https://www.npmjs.com/package/html-webpack-plugin) 
```javascript
new HtmlWebpackPlugin({
		template: path.join(__dirname, 'src/search.html'), // 模板路径
		filename: 'search.html', // 文件名称
		chunks: ['search'], // 生成的html用哪些chunk
		inject: true, // 自动注入
		minify: {
				html5: true,
				collapseWhitespace: true,
				preserveLineBreaks: false,
				minifyCSS: true,
				minifyJS: true,
				removeComments: false
		}
}) 
```

## 自动清理理构建目录
 - npm install aotoprefixer -D
 - npm install postcss-loader -D
 ```javascript
{
		test: /.less$/,
		use: [
				'css-loader',
				'less-loader',
				{
						loader: 'postcss-loader',
						options: {
								plugins: () => [
										require('autoprefixer')({
												browsers: ['last 2 version', '>1%', 'ios 7']
										})
								]
						}
				},
		]
},
 ```

## 资源内联的意义
1. raw-loader 内联 html
- `注意版本问题` npm install raw-loader@0.5.1
```javascript
<script>${require('raw-loader!babel-loader!. /meta.html')}</script>
```

2. raw-loader 内联 JS
```javascript
<script>${require('raw-loader!babel-loader!../node_modules/lib-flexible')}</script>
```

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
- 只在weback3的时候才可使用
- [ExtractTextplugin](https://www.npmjs.com/package/extract-text-webpack-plugin)

## mini-css-extract-plugin - css提取
- webpack4之后开始使用此插件
```javascript
// 下载
npm install mini-css-extract-plugin -D
// webpack中引用
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
// 使用时候需要在俩处配置 
// 1. 规则中
{
    test: /\.(less|css)$/,
    use: [
        {
            loader: MiniCssExtractPlugin.loader,
        },
        'css-loader',
        {
            loader: 'less-loader',
            options: {
                lessOptions: {
                    javascriptEnabled: true,
                },
            }
        }
    ],
},
// 2. 插件中
new MiniCssExtractPlugin({
    filename: 'css/base.css',
    chunkFilename: '[id].css',
}),
```

# webpack解析器
## webpack-loader
- [loader](https://www.webpackjs.com/loaders/url-loader/)官网会发现loader大概有7个模块,**1、文件，2、JSON，3、转换编译，4、模板，5、样式，6、清理和测试，7、框架**
- webpack因为只识别`js`文件，所以需要这些loader来处理文件转成js,个个大神的开源代码和维护都在社区有明显标注
- 常规选项配置
1. test: 要检测的文件
2. loader: 要使用的插件
3. include: 解析包含的文件
4. exclude: 解析要排除的文件
5. options: 插件高级配置选项
6. use: [] 多个插件配置时使用

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

### css-loader
- 代码如下！ [文档](https://www.npmjs.com/package/css-loader)
```javascript
// index.js
import '../css/base.css';
```
```css
// base.less
#phone {
    background-image: url('../assets/actrulebtn.png');
}
```
- 问题：在入口文件引入css时,css-loader将url自动处理了，并在`publickpath`中输出了图片,自信看文档有个url选项，是否修改url配置，当设置`url:false`时，不会做额外处理。
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        loader: "css-loader",
        options: {
          url: false,
        },
      },
    ],
  },
};
```


## 规范代码
[esLint官网](https://eslint.bootcss.com/docs/user-guide/configuring)
- 新增`.eslintrc.js`文件
```javascript
module.exports = {
    "parser": "babel-eslint", // 使用解析器
    "extends": "airbnb",  // 继承airbnb
    // 当前想启用的环境
    "env": {
        "browser": true,
        "node": true
    },
    // 定义规则
    "rules": {
        "indent": ["error", 4]
    }
};
```
- npm 下载
```javascript
"babel-eslint": "^10.0.1",
"eslint": "^5.16.0",
"eslint-config-airbnb": "^17.1.0",
"eslint-config-airbnb-base": "^13.1.0",
"eslint-loader": "^2.1.2",
"eslint-plugin-import": "^2.17.3",
"eslint-plugin-jsx-a11y": "^6.2.1",
"eslint-plugin-react": "^7.13.0",
```


## webpack如何暴露库