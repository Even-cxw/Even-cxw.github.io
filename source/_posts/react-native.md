---
title: react-native
date: 2023-10-17 11:22:24
categories: react-native
tags: [react-native]
comments: false
---

# 环境搭建

## 规避版本冲突
1. 查看版本 `react-native -v`
2. 删除依赖 `npm uninstall -g react-naitve-cli @react-native-community/cli`

## 包管理工具
1. 不可使用`cnpm`, packager不能正常识别。

## 软件

### mac
> 同步更新最新版本
1. [Xcode使用与介绍](https://www.bilibili.com/video/BV15r4y1H7PN/?spm_id_from=333.337.search-card.all.click&vd_source=2afde20dcb23008f4b5372488743205d)

### window
1. andro




### mac hundletool工具
1. brew install hundletool

- hundletool version查看报错 `没有找到java路径`
```s
# 输出如下 - 
/Users/even/Library/Android/bundletool/@@HOMEBREW_JAVA@@/bin/java: No such file or directory
/opt/homebrew/bin/bundletool: line 3: exec: /Users/even/Library/Android/bundletool/@@HOMEBREW_JAVA@@/bin/java: cannot execute: No such file or directory
```

> 问题排查：查看java是否存在
- `java -version` 查看java版本
```s
# 输出如下 - 提示已经安装了OpenJDK 11
openjdk version "11.0.20.1" 2023-08-24 LTS
OpenJDK Runtime Environment Zulu11.66+19-CA (build 11.0.20.1+1-LTS)
OpenJDK 64-Bit Server VM Zulu11.66+19-CA (build 11.0.20.1+1-LTS, mixed mode)
```

- `/usr/libexec/java_home -v 11` 查看java11路径
```
# 11路径如下
/Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home
```
- `export JAVA_HOME="/Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home"` 设置java路径


2. hundletool使用
- 将abb转成apk
```s
bundletool build-apks --bundle=/Users/even/Documents/app/application-e6205b95-ed5f-439f-8600-0b6f6b4dade3.aab --output=/Users/even/Documents/app/Even.apks
```

```s
bundletool install-apks --apks=/Users/even/Documents/app/Even.apks --output-dir=/Users/even/Documents

bundletool install-apks --apks=path_to_output_directory/output.apks --output-dir=build
bundletool install-apks --apks=/Users/even/Documents/app/Even.apks  --output-dir build

/var/folders/j5/3s02cvh54hbbqs9yxfvzk06h0000gn/T/3777498629724160818

adb install /Users/even/Documents/app/Even.apks
adb install-multiple /Users/even/Documents/app/Even.apks

```