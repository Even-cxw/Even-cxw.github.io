---
title: 资源下载
date: 2021-11-22 16:15:24
categories: javascript
tags: [javascript]
comments: false
---

## 下载逻辑 - <font color="red">先区分是什么请求方式</font>
###  get请求
```javascript
// 直接打开下载
window.open()
```
### post请求

1. application/octet-stream格式
	-  application/octet-stream: 需要转成[blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob)对象，在通过[createObjectURL](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL)转成连接。
	```javascript
	var blob = new Blob(['1111'],{type:'application/vnd.ms-excel'})
	var link = URL.createObjectURL(blob)
	let a = document.createElement("a"); // 生成一个a元素
	let event = new MouseEvent("click"); // 创建一个单击事件
	a.download = "text.txt"; // 设置图片名称
	a.href = link // 将生成的URL设置为a.href属性
	a.dispatchEvent(event); // 触发a的单击事件
	```

<!-- more -->


2. base64格式：若后端未返回前缀需要我们自己拼接，拼接好之后直接当url处理即可。拼接如下！！
	- 文档
	|  文件后缀   | 文档类型  |
	|  --------  | --------  |
	| doc  | data:application/msword;base64, |
	| docx  | data:application/vnd.openxmlformats-officedocument.wordprocessingml.document;base64, |
	| xls  | data:application/vnd.ms-excel;base64, |
	| xlsx  | data:application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;base64, |
	| pdf  | data:application/pdf;base64, |
	| ppt  | data:application/vnd.ms-powerpoint;base64, |
	| pptx  | data:application/vnd.openxmlformats-officedocument.presentationml.presentation;base64, |
	| txt  | data:text/plain;base64, |
	
	- 图片
	|  文件后缀   | 文档类型  |
	|  --------  | --------  |
	| png  | data:image/png;base64, |
	| jpg  | data:image/jpeg;base64, |
	| gif  | data:image/gif;base64, |
	| svg  | data:image/svg+xml;base64, |
	| ico  | data:image/x-icon;base64, |
	| bmp  | data:image/bmp;base64, |
	- 拼接好base64-得到新的base64，我们直接当做url处理即可,代码如下
	> a标签下载
	```javascript   
		let a = document.createElement("a"); // 生成一个a元素
		let event = new MouseEvent("click"); // 创建一个单击事件
		a.download = "text.txt"; // 设置图片名称
		a.href = link //  将生成的URL设置为a.href属性
		a.dispatchEvent(event); // 触发a的单击事件
	```
	> wondow.open()下载
	```javascript
		windo.open()
	```


## 了解Blob对象
- 第一个参数：ArrayBuffer, ArrayBufferView, Blob, DOMString 等对象构成的 Array 
- 第二个参数：[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob)有说是[MIME](https://www.w3school.com.cn/media/media_mimeref.asp)媒体类型，
- 我的个人理解：<font color="red">【定义下载文件的后缀名】</font>，下载的时候直会按照我们定义的下载。 

## 问题?
### blob对象MIME参数可以定义后缀名，a标签的download也可以定义后缀名，哪个优先级更高?
> `download`的优先级更高
> download没有定义后缀名的时候以[MIME](https://www.w3school.com.cn/media/media_mimeref.asp)为准
```javascript
var blob = new Blob(['1111'],{type:'application/vnd.ms-excel'}) //定义了exce表格
a.download = "text.txt"; // 定义了txt文本
```

### 改变后缀名文件大小是否被改变?
> 如下分别定义了`text.jpg`、`vscode.png`、`text.text`三种不同的名字,转成了我想要的格式，但是他的**数据大小是不会被改变的**
![](/images/download/download01.png)

## 实际开发应用
### 图片连接不在同一个域下,如何实现下载？
1. 我们需要吧连接转成barse64然后在下载
2. barse64可以用canvas的drawImage转成baser64,drawImage的参数是img
3. 所以我们需要把url连接变成image对象。 代码如下！！
```javascript
var img = new Image();
img.crossOrigin = 'anonymous'
img.src = '.....';
img.onload = function() {
	let canvas = document.createElement('canvas');
	canvas.width = img.width;
	canvas.height = img.height;
	let content = canvas.getContext('2d');
	content.drawImage(img,0,0,img.width,img.height);
	let url = canvas.toDataURL();
	windo.open(url)
}
```

### 如何实现input框手动输入内容，并下载？
1. 第一想到（a连接、window.open）实现下载，那么如何将a连接的href指向内容呢？
2. 可能想到了用 URL.createObjectURL()创建连接，但是该函数的参数只能是 blob\file对象
3. blob对象只要new一个即可，将我们要输入的文本以参数的形式传递进去，实例化一个blob对象。
4. 通过以上想法，便可以实现这个功能。  代码如下！ 有俩种可实行方案。
```javascript
let text = document.getElementById("text");
let download = document.getElementById("download");
text.addEventListener('input', function() {
	console.log(this.value)
})
download.addEventListener('click', function() {
	var blob = new Blob(['1111'],{type:'application/vnd.ms-excel'})
	console.log(blob);
	var link = URL.createObjectURL(blob)
	// window.open(link)
	let a = document.createElement("a"); // 生成一个a元素
	let event = new MouseEvent("click"); // 创建一个单击事件
	a.download = "text.txt"; // 设置图片名称
	a.href = link // 将生成的URL设置为a.href属性
	a.dispatchEvent(event); // 触发a的单击事件
})
```