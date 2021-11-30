---
title: 隐式类型转换、原始值、引用值
date: 2021-11-30 14:30:24
categories: javascript
tags: [javascript]
comments: false
---

## 原始值
- 在 ECMAScript 中，变量可以存在两种类型的值，即[原始值]((https://www.w3school.com.cn/js/pro_js_value.asp))和[引用值](https://www.w3school.com.cn/js/pro_js_value.asp),`这里就不做过多解释了。`
- 原始值有`5种数据类型`，**undefined**、**null**、**boolean**、**string**、**number**
- 引用值有`俩种引用类型`，**Object**、**Array**

## valueOf()、toString()方法解析
- [valueOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf)、[toString()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString) : `返回值为该对象的原始值。`

- 那么5中基本数据类型的原始值分别是什么呢?
```javascript
// null undefined 不存在原始值
var a = null;
console.log('a.valueOf()',a.valueOf()) // 报错
console.log('a.toString()', a.toString()) // 报错

var b = undefined;
console.log('a.valueOf()',a.valueOf()) // 报错
console.log('a.toString()', a.toString()) // 报错

var c = new Number('123')
console.log('Number.valueOf()',c.valueOf())  // 123
console.log('Number.toString()', c.toString())  // '123'

var d = new String('12df');
console.log('String.valueOf()',typeof d.valueOf()) // '12df'
console.log('String.toString()', d.toString()) // '12df'

var e = new Boolean('fd');
console.log('Boolean.valueOf()', e.valueOf()) // true
console.log('Boolean.toString()', e.toString()) // true

```
> 由此可见除了`undefined`、`null`报错外，其他都返回自己本身。

## 引用值转成原始值
- `为什么`引用值要转成原始值呢？：因为在`隐式类型转换`的时候出现引用类型，javascript会吧`他`转成原始值进行`比较`。
```javascript
var f = new Array();
console.log('Array.valueOf()',f.valueOf())  // []
console.log('Array.toString()', f.toString())  // ''

var e = new Array(3);
console.log('Array.valueOf()',e.valueOf())  // [3]
console.log('Array.toString()', e.toString())  // '3'

var g = new Array(1,2,3);
console.log('Array.valueOf()',g.valueOf())  // [1, 2, 3]
console.log('Array.toString()', g.toString())  // '1,2,3'

var j = new Object({});
console.log('Object.valueOf()',j.valueOf())  // {}
console.log('Object.toString()', j.toString())  // '[object Object]'
```
- 大家可以发现规律`valueOf()`一般都返回自己本身，而`toString()`转成string比较傻帽，铺垫已做好那么开始看**正题**。

<!--more-->

## 隐式类型转换
### `Boolen()`、`if()`、`!!`
- 以上操作都会强制转换成布尔类型，比较`强横`,只要记住以下转成false即可！！！
| 值 |  转换符 |  结果  |
| -- | ----  | --- |
| ""  | Boolen("") | false |
| 0 | Boolen(+0) | false |
| false  | Boolen(false) | false |
| undefined  | Boolen(undefined) | false |
| null  | Boolen(null) | false |
| NAN  | Boolen(NAN) | false |

- 其他均转成true
```javascript
console.log('Boolean([])',Boolean([])) // true
console.log('Boolean({})',Boolean({})) // true
```

### `Number()`
> Number类型转换就比较`复杂`.
#### 1、基本数据类型转成Number,
- Number类型转Number,保持原有值
```javascript
console.log('Number(1)',Number(1)) // 1
```
- Boolen类型转Number
```javascript
console.log('Number(true)',Number(true)) // 1 
console.log('Number(false)',Number(false)) // 0
```
- undefined、null类型转Number
```javascript
console.log('Number(undefined)',Number(undefined)) // NaN
console.log('Number(null)',Number(null)) // 0
```
- String类型转Number
```javascript
console.log('Number(空字符串)',Number('')) // 0
console.log('Number(字符串123)',Number('123')) // 123
console.log('Number(asdf)',Number('asdf')) // NaN
```

#### 2、引用数据类型转成Number
- 除了转Boolen类型的时候是特殊的，其他类型转换需要将引用数据类型先转成`原始值`在转成`Number`。
> <font color="red">【原始值查找规则：如果valueOf有值返回valueOf,如果没有查找toString。 基本数据类型一般找valueOf,引用数据类型找toString】</font>
```javascript
console.log('Number([])',Number([])) // 0
// toString原始值 "" 转成数组 0
console.log('Number([4])',Number([4])) // 4
// toString原始值 4 转成数组 4
console.log('Number([1,2])',Number([1,2])) // NaN
// toString原始值 '1,2' 转成数组 NaN
console.log('Number([a])',Number(['a'])) // NaN
// toString原始值 'a' 转成数组 NaN
console.log('Number({})',Number({})) // NaN
// toString原始值 [object Object]  NaN
console.log('Number({a:1})',Number({a:1})) // NaN
// toString原始值 [object Object]  NaN
```

### `==`

- `==` 我们在代码中总会碰到，但是一般都是简单的比较，他在比较中又做了哪些转换呢？`请记住一下4点转换规则`

1. `两边如果存在null或者undefined，他们只和对方以及**自身相等**，和其他的都不等`
```javascript
console.log('null == undefined', null == undefined) // true
console.log('null == false',null == false) // false
console.log('null == 0 ', null == 0 ) // false
```

2. `如果等式两边有布尔类型，要将布尔类型转化成数字`
```javascript
console.log('false == ""',false == "") // true
```
> 1. Number(false) // 0
> 2. Number("") // 0

3. `如果等式两边有数字，就要将不是数字的那边转化成数字`
```javascript
console.log('false == ""',false == "") // true
```

4. `如果等式两边有引用类型存在，需要将引用类型转换为**原始值**,调用**ToPrimitive**方法.`
```javascript
false == [] // true
```
> 1. []的原始值`''`, 就变成了 false == ''
> 2. false == '' 转成Number类型 0 == 0


## 练习题

- 如果下面练习题作对了，说明你真的理解`隐式转换了`

```javascript
console.log('false == [1]',false == [1]) 
console.log('false == [a]',false == ['a'])
console.log('false == {}',false == {})
```

- 揭晓答案

```javascript
console.log('false == []',false == []) 
```

> 1. [1]的原始值`toString([1])` => `1`, 简化为 `false == 1`
> 2. false转成Number,`Number(false)` => `0`  所以 0 == 1  是`false`

```javascript
console.log('false == [a]',false == ['a']) 
```
> 1. ['a']的原始值`toString([a])` => `'a'`, 简化为 `false == 'a'`
> 2. false转成Number,`Number(false)` => `0`  简化为 0 == 'a' 
> 3. 'a'转成Number, `Number('a')` => `NaN` 所以 0 == NaN 是`false`

```javascript
console.log('false == {}',false == {})
```
> 1. ['a']的原始值`toString({})` => `[object,object]`, 简化为 `false == '[object,object]'`
> 2. false转成Number,`Number(false)` => `0`  简化为 0 == 'a' 
> 3. 'a'转成Number, `Number('[object,object]')` => `NaN` 所以 0 == NaN 是`false`