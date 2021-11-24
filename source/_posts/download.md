---
title: 资源下载
date: 2021-11-22 16:15:24
categories: javascript
tags: [javascript]
comments: false
---

## 一、下载逻辑
<font color="red">【先区分是请求方式】</font>

### 1、get请求
```javascript
// 直接打开下载
window.open()
```
### 2、post请求

1. 若后端返回数据为`application/octet-stream格式`
- application/octet-stream: 需要转成[blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob)对象，在通过[createObjectURL](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL)转成连接。
```javascript
// res为后端返回的数据流
var blob = new Blob(['res'],{type:'application/vnd.ms-excel'})
var link = URL.createObjectURL(blob)
let a = document.createElement("a"); // 生成一个a元素
let event = new MouseEvent("click"); // 创建一个单击事件
a.download = "text.txt"; // 设置图片名称
a.href = link // 将生成的URL设置为a.href属性
a.dispatchEvent(event); // 触发a的单击事件
```

<!-- more -->


2. 若后端返回格式为`base64格式`：可能需要我们拼接前缀，对应前缀如下。	
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
- 将拼接好的url通过a连接下载，代码如下！
> a标签下载
```javascript   
	let a = document.createElement("a"); // 生成一个a元素
	let event = new MouseEvent("click"); // 创建一个单击事件
	a.download = "text.txt"; // 设置图片名称
	a.href = link //  将生成的base64设置为a.href属性
	a.dispatchEvent(event); // 触发a的单击事件
```
> wondow.open()下载
```javascript
	windo.open()
```

----

## 二、Blob对象
**1、第一个参数：**[ArrayBuffer](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)、[TypedArray](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)、[Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob)、 DOMString:javascript里的string字符串 等对象构成的 Array,
- 为了让前端操作二进制数据才有了`ArrayBuffer`、`TypedArray`、`Uint8Array...`等对象， 那他们分别代表什么意思呢？
- `ArrayBuffer`
> 官网解释：二进制数据缓冲区，它是一个字节数组。 个人理解为：无法读取和操作的数据格式，只能看到他的长度看不到内容更别说来`操作此内容`，<font color="green">【那我们来如何操作呢？就有了另外几个api】</font>
```javascript
// 定义长度为4的数据容器, 哈哈，这里只能看不能有任何操作！
var buffer = new Arraybuffer(4)
```
- `TypedArray`
> 这个更是一个`烟雾弹`,TypedArray既不是全局变量也不是函数(所以休想在全局中访问到他),他是`Uint8Array...`等子类的实例化，当我们得到`TypedArray`实例后便可以轻松的使用他的api操作数据了。
```javascript
// 定义长度为4的数据容器, 哈哈，这里只能看不能有任何操作！
var buffer = new Arraybuffer(4)
// 得到TypedArray 这样我就可以操作上面的数据了。
var TypedArray = new Uint8Array(buffer);
TypedArray[0] = 32;
TypedArray[1] = 64;
```
- `Uint8Array`
> 数组类型表示一个8位无符号整型数组，创建时内容被初始化为0。创建完后，可以以对象的方式或使用数组下标索引的方式引用数组中的元素。	
```javascript 
// 创建TypedArray实例
var TypedArray = new Uint8Array(object);
```

**2、第二个参数：**[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob/Blob)有说是[MIME](https://www.w3school.com.cn/media/media_mimeref.asp)媒体类型，白话意思:"定义下载文件的后缀名"

----

## 三、问题?
### 1、blob对象MIME参数可以定义后缀名，a标签的download也可以定义后缀名，哪个优先级更高?
> `download`的优先级更高
> download没有定义后缀名的时候以[MIME](https://www.w3school.com.cn/media/media_mimeref.asp)为准
```javascript
var blob = new Blob(['1111'],{type:'application/vnd.ms-excel'}) //定义了exce表格
a.download = "text.txt"; // 定义了txt文本
```

### 2、改变后缀名文件大小是否被改变?
> 如下分别定义了`text.jpg`、`vscode.png`、`text.text`三种不同的名字,转成了我想要的格式，但是他的**数据大小是不会被改变的**
![](/images/download/download01.png)

----

## 四、实际开发应用
### 1、图片连接不在同一个域下,如何实现下载？
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

### 2、如何实现input框手动输入内容，并下载？
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
	let a = document.createElement("a"); // 生成一个a元素
	let event = new MouseEvent("click"); // 创建一个单击事件
	a.download = "text.txt"; // 设置图片名称
	a.href = link // 将生成的URL设置为a.href属性
	a.dispatchEvent(event); // 触发a的单击事件
})
```

### 3、如何实现base64转成blob？
- `new Blob(array,options)`, 思考:从base64中获取Blob中获取`俩个参数`，如果获取到直接new一下就完美解决！！!
1. <font color="green">[获取第二个参数]]</font>：<font color="red">MIME类型很好获取 => 可以从base64前缀中读取到。	</font>
| base64前缀 | MIME类型 |
| ---- | ---- |
|   data:application/msword;base64,   |  application/msword    |
|   data:application/vnd.ms-powerpoint;base64,   |  application/vnd.ms-powerpoint   |
> 代码如下
```javascript
// mime类型
var mimeType = base64URI.split(',')[0].split(':')[1].split(';')[0]; 
```

2. <font color="green">[获取第一个参数]</font>：<font color="red">将base64内容转成TypeArray</font>
> 解码base64-api:[atob](https://developer.mozilla.org/en-US/docs/Web/API/atob)、编码base64-api:[btoa](https://developer.mozilla.org/en-US/docs/Web/API/btoa)、[charCodeAt](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt)
```javascript
function base64ToBlob() {
	// mime类型
	var mimeType = base64URI.split(',')[0].split(':')[1].split(';')[0]; 
	// 解码base64
	var atobBase64 = atob(base64URI.split(',')[1]); //base64 解码
	// 创建二进制缓冲区，只能看不能操作
	var arrayBuffer = new ArrayBuffer(atobBase64.length);
	// 创建可以操作视图缓冲区数据的函数api - 只有这样才可以操作数据。
	var typeArray01 = new Uint8Array(arrayBuffer)
	// 操作类型数组，将解码barse64数据插入类数组中。
	for (var i = 0; i < atobBase64.length; i++) {
		typeArray01[i] = atobBase64.charCodeAt(i);
	}
	new Blob([typeArray01], {type: mimeType});
}
```


### 4、如何实现blob转成base64？
- [FileReader](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader)
```javascript
var reader = new FileReader();
reader.onload = function (e) {
	callback(e.target.result);
}
reader.readAsDataURL(blob);

```