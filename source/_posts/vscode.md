---
title: 通过终端命令打开vscode编辑器
date: 2021-11-16 14:30:24
categories: vscode
tags: [vscode]
comments: false
---



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