---
title: 解析URL
date: 2022-04-11 16:15:24
categories: javascript
tags: [javascript]
comments: false
---

## URLSearchParams API
- [URLSearchParams](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams)接口定义了一些实用的方法来处理URL
- 但是```不会解析完整 URL```，需要传入类似location.search 但是如果字符串起始位置有 ? 的话会被去除。

```js
var paramsString1 = "http://example.com/search?query=%40";
var searchParams1 = new URLSearchParams(paramsString1);

searchParams1.has("query"); // false
searchParams1.has("http://example.com/search?query"); // true

searchParams1.get("query"); // null
searchParams1.get("http://example.com/search?query"); // "@" (equivalent to decodeURIComponent('%40'))

var paramsString2 = "?query=value";
var searchParams2 = new URLSearchParams(paramsString2);
searchParams2.has("query"); // true

var url = new URL("http://example.com/search?query=%40");
var searchParams3 = new URLSearchParams(url.search);
searchParams3.has("query") // true
```

----

## 方法一：将URL参数解析成对象

```js
/**
 * @desc 将URL参数解析成对象
 * @param {string} url 1. url=https://www.baodu.com?nihao=111 2. ?nihao=111
 * @returns {object}
 */
function parseURLToObj(url = '') {
    if (!url) throw new Error('url参数错误');
    let search = url.indexOf('?') ? url.split('?')[1] : url;
    let params = {};
    for (const [key, value] of new URLSearchParams(search)) {
        params[key] = value;
    }
    return params;
}
```

## 方法二：将对象解析成URL字符串

```javascript
/**
 * @desc 将对象解析成URL字符串
 * @param {object} params 
 * @returns {string}
 */
function parseObjToString(params = {}) {
    let search = new URLSearchParams('');
    Object.keys(params).forEach(key => {
        search.append(key, params[key]);
    })
    return search.toString();
}

```

<!--more-->

## 方法三：向URL中添加某个参数

```js
/**
 * @desc 向URL中添加某个参数
 * @param {string} url 
 * @param {object} params 
 * @returns {string}
 */
function URLAddParams(url, params) {
    let search = parseObjToString(params)
    return url.indexOf('?') == -1 ? `${url}?${search}` : `${url}&${search}`
}
```

## 方法四：判断URL中是否有某个参数

```js
/**
 * @desc 判断URL中是否有某个参数
 * @param {string} url 
 * @param {string} key 
 * @returns {boolean}
 */
static URLHasKey(url,key) {
    if (!url) throw new Error('url参数错误');
    let search = url.indexOf('?') ? url.split('?')[1] : url;
    return new URLSearchParams(search).has(key);
}

```

## 方法五：判断URL中是否有value值

```js
/**
 * @desc 判断URL中是否有value值
 * @param {string} url 
 * @param {string} value 
 * @returns {boolean}
 */
static URLHasValue(url,value) {
    if (!url) throw new Error('url参数错误');
    let search = url.indexOf('?') ? url.split('?')[1] : url;
    var params = new URLSearchParams(search);
    for(var paramsValue of params.values()) {
        if (paramsValue === value) {
            return true;
        }
    }
    return false;
}

```