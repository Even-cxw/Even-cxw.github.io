---
title: eval() 与 new Function()
date: 2021-12-02 16:15:24
categories: javascript
tags: [javascript]
comments: false
---

> 俩者其实根本没有可比性，只不过都是将传入的字符串当做 JavaScript 代码进行执行。所以这篇文章并不会很长。

## [eval()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/eval)
- 是否有这样需求，一个`'function add(a,b) {retrun a+b}'`字符串传递给你，让你执行里面的函数，
```javascript
// 后端返回 字符串函数
let addstr =  'function add(a,b) {retrun a+b}'
eval(addstr); // 相当于生命函数add
add(); // 执行add函数
```
- 或者给你抛出一个变量`a = add`或 `a = sum`,`a`是可变的，分别执行add函数，或sum函数。
```javascript
function a() {
    console.log('我是a函数')
}
function b() {
    console.log('我是b函数')
}
let funName = 'a';
eval(funName+'()') // 字符串拼接成 => a();
// 或者 第二种
eval('('+funName+')')(); 
```
> 第二种大家可能不理解，为什么要加()
> 1. 字符串拼接后相当于执行了eval里面的`function a() {console.log('我是a函数')}`
> 2. `function a() {console.log('我是a函数')}()`这样调用肯定报错，所以要在函数外面加一下`（）`
> 3. `(function a() {console.log('我是a函数')})()`

<!--more-->

## [new Function()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function)

- 他的使用跟eval还是有一定的差异。
> `所有的`参数类型都是`string`，最后一个代表着函数体里要执行的内容，切记`多个表达式`要用`;`分开;

```javascript
// 声明一个函数普通写法
function add(a,b) {
    console.log('我是add函数')
    return a + b;
}
// 使用new Function（）声明一个sum函数,实现跟add一样的功能
let sum = new Function('a','b',"console.log('我是sum函数');return a+b;")
sum(1,2) == add(1,2); // true;
```

## 区别

- eval中的代码执行时的作用域为当前作用域。它可以访问到函数中的局部变量。
- new Function中的代码执行时的作用域为全局作用域，不论它的在哪个地方调用的。所以它访问的是全局变量a。它根本无法访问b函数内的局部变量。

```javascript
var a = 'global scope'
function b(){
    var a = 'local scope'
    eval('console.log(a)') //local scope
    (new Function('','console.log(a)'))() //global scope
}
b()
```