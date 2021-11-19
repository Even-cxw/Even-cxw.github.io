---
title: 资源下载
date: 2021-11-18 16:15:24
categories: javascript
tags: [javascript]
comments: false
---

> 浏览器不仅对请求有同源策略的要求，资源的下载也是一样的。

# 连接 - 同域
- **点击a连接下载**
> 在a连接的基础上加`download`属性: 定义下载的文件名
```javascript
// a连接可以是全域名
<a href="https://fanyi-cdn.com/wechatQr_68e950b.png" download="text">a标签下载文本</a>
// 也可以是相对路径
<a href="https://fanyi-cdn.com/wechatQr_68e950b.png" download="text">a标签下载文本</a>
```
> download: 不仅可以自定义名字，也可以自定义后缀。 比如你想让一个图片变成photo.text后缀也是可以的。
> 如下分别定义了`text.jpg`、`vscode.png`、`text.text`三种不同的名字,转成了我想要的格式，但是他的**数据大小是不会被改变的**
![](/images/download/download01.png)

- 被动触发(eg: 如点击一个按钮，同时下载多个图片)
> 这种场景不可能让用户点击多次，
```javascript
// 手动创建多个标签
let a = document.createElement("a"); // 生成一个a元素
let event = new MouseEvent("click"); // 创建一个单击事件
a.download = "text"; // 设置图片名称
a.href = './src/text.text'; // 将生成的URL设置为a.href属性
a.dispatchEvent(event); // 触发a的单击事件
```


# 数据流

