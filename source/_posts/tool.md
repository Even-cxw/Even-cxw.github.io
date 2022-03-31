---
title: 常用工具
date: 2020-11-16 14:30:24
categories: vscode
tags: [vscode，nvm]
comments: false
---

# nvm常用命令
---

- 切换成默认版本 `nvm alias default v12.7.0`
- 下载指定版本node `nvm install v12.7.0`

- nvm install报错 `could not retrieve`
```javascript
// 解决办法
// 找到nvm配置目录 、settings.text文件，添加如下配置
node_mirror:npm.taobao.org/mirrors/node/
npm_mirror:npm.taobao.org/mirrors/npm/
```


# cnpm安装
```javascript
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

- 替换淘宝镜像
```javascript
// 命令
npm config set registry https://registry.npm.taobao.org
// 查看是否成功
npm config get registry
```

# vscode配置
---
- vscode下载
- [VScode官方](https://code.visualstudio.com/)因为连接境外下载速度很慢，[解决方案](https://blog.csdn.net/m0_55548148/article/details/115587339)
```javascript
//外网地址：https://az764295.vo.msecnd.net/stable/3a6960b964327f0e3882ce18fcebd07ed191b316/VSCode-darwin-universal.zip
//内网地址：https://vscode.cdn.azure.cn/stable/3a6960b964327f0e3882ce18fcebd07ed191b316/VSCode-darwin-universal.zip
//替换连接：vscode.cdn.azure.cn
```

- mac笔记本
1. 打开vscode
2. `command + shift + p `打开命令面板
3. 输入shell（选择"install code command in PATH"）<font color="green">【如下图】</font>
4. 打开终端输入命令：`code <dir> ` (dir - 对应的文件夹路径)

<!-- more -->

![](/images/vscode.png)
------

- window笔记本
1. 打开vscode安装位置，进入bin文件夹，复制路径 eg：`E:\Microsoft VS Code\bin；`
2. 回到桌面，右键我的电脑，高级系统设置 ，环境变量，编辑path值，在原来的path后面加上eg ;`E:\Microsoft VS Code\bin`(英文的分号+vscode的bin路径)
3. 打开终端输入命令：`code <dir> ` (dir - 对应的文件夹路径)


- vscode常用插件
- Live Serve : 在浏览器中打开index.html，自动热更新
- typeScript Auto Compiler : 自动转成js文件



# node问题

- npm ERR! cb() never called!