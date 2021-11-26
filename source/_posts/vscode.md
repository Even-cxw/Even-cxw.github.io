---
title: vscode编辑器
date: 2021-11-16 14:30:24
categories: vscode
tags: [vscode]
comments: false
---

## vscode下载
- [VScode官方](https://code.visualstudio.com/)因为连接境外下载速度很慢，[解决方案](https://blog.csdn.net/m0_55548148/article/details/115587339)
```javascript
//外网地址：https://az764295.vo.msecnd.net/stable/3a6960b964327f0e3882ce18fcebd07ed191b316/VSCode-darwin-universal.zip
//内网地址：https://vscode.cdn.azure.cn/stable/3a6960b964327f0e3882ce18fcebd07ed191b316/VSCode-darwin-universal.zip
//替换连接：vscode.cdn.azure.cn
```

##  mac笔记本
1. 打开vscode
2. `command + shift + p `打开命令面板
3. 输入shell（选择"install code command in PATH"）<font color="green">【如下图】</font>
4. 打开终端输入命令：`code <dir> ` (dir - 对应的文件夹路径)

<!-- more -->

![](/images/vscode.png)
------

## window笔记本
1. 打开vscode安装位置，进入bin文件夹，复制路径 eg：`E:\Microsoft VS Code\bin；`
2. 回到桌面，右键我的电脑，高级系统设置 ，环境变量，编辑path值，在原来的path后面加上eg ;`E:\Microsoft VS Code\bin`(英文的分号+vscode的bin路径)
3. 打开终端输入命令：`code <dir> ` (dir - 对应的文件夹路径)


## vscode常用插件
- Live Serve : 在浏览器中打开index.html，自动热更新
- typeScript Auto Compiler : 自动转成js文件