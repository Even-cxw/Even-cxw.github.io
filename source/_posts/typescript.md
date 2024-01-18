---
title: typescript笔记
date: 2022-04-24 14:30:24
categories: typescript
tags: [typescript]
comments: false
---

## 基本数据类型

- number数字类型
```ts
// js写法
let num = 20;
num = 23;
// ts写法
let num: number = 25
```

- boolean布尔类型
```ts
// boolean
let isLogin:boolean = true;
```

- string字符串类型
```ts
let name:string = 'Even'
```

- null undefined类型
```ts
let a:null = null;
let b:undefined = undefined;
```

<!--more-->

## 引用数据类型

- 数组类型
```ts
// 三种书写方法
let names01:Array<String> = ['hello'];
let names02:String[] = ['hello'];
let name03:any[] = ['hello']

// 元组 - 确切知道数组里每个值的类型
let colors: [String,Number] = ['hell',12];

// 枚举
enum Color {
  number,
  yellow,
  red,
}
let myColor:Color = Color.red; // 获取角标
let myColorVal:String = Color[myColor]; // 获取值
console.log(myColor);
console.log(myColorVal);
```

- 对象类型
```ts
let dataObj:{age:number, name:String} = {
  age: 12,
  name: 'Even'
}
dataObj = {
  age: 12,
  name: 'levi'
}

```

## typescript中的其他类型

- ts其他类型 

### never类型：`容错`
```ts
// never的应用，抛出异常
let y:number = 1;
y = (()=>{throw new Error('errow')})()
```

### any、unknow
```ts
// any不会做语法检查
let name:any = {}
name = 'Even'
// unknow会做语法检查
let age:unknow = 23
// let c = age.info 报错
```

### `泛型（Generics）`
- 泛型：是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候在指定类型的一种特性 

1. 泛型在`函数`中使用，我们在函数名后添加`<T>`
```ts
// 泛型也可以指定默认值
function createArray<T=number>(length: number, value: T): Array<T> {
  let result: Array<T> = []
  for (let i = 0; i < length; i++) {
    result[i] = value
  }
  return result
}
let result = createArray<string>(3, 'x')
console.log(result) // ['x', 'x', 'x']
let result2 = createArray<number>(3, 3) // T 就相当于一个参数，传什么是什么
console.log(result2) // [3, 3, 3]
```

2. 泛型在`类`中的使用
```ts
class MyArray<T> {
  private list: T[] = []
  add(val: T) {
    this.list.push(val)
  }
  getMax(): T {
    let result: T = this.list[0]
    for (let i = 0; i < this.list.length; i++) {
      if (this.list[i] > result) {
        result = this.list[i]
      }
    }
    return result
  }
}
let arr = new MyArray<number>()
arr.add(1)
arr.add(2)
arr.add(3)
let result3 = arr.getMax()
console.log(result3) // 3
```

3. 泛型在`接口`中的使用
```ts
interface CreateArrayFunc<T> {
  (length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc<string>; // 使用泛型接口的时候，需要定义泛型的类型。
createArray = function<T>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
      result[i] = value;
  }
  return result;
}

let result = createArray(3, 'x'); 
console.log(result)// ['x', 'x', 'x']
```

4. 泛型的`约束`
```ts
interface LengthWith {
  length: number
}
function logger<T extends LengthWith>(val: T) {
  console.log(val.length) // 5
  return val;
}
logger('金色小芝麻')

```

### record记录
- 可以简化代码
> ，Record 是一个泛型工具类型，用于创建一个对象类型，其中包含指定属性的键值对。
```ts
type studentScore = { [ name:string]:number }
// 也可以用Record简化
type studentScore = Record<string, number>
```

### Partial部分的
- 简化代码
```ts
type Coord = Partial<Record<'x' | 'y', number>>;
// 等同于
type Coord = {
	x?: number;
	y?: number;
}
```

### keyof
```ts
type Point = { x: number; y: number };
type P = keyof Point; //p的值可以为 'x' | 'y'
const a:P = 'x'; 
```

### typeof
```ts
const obj = { x: 10, y: 3 }
type A = typeof obj;
const a: A = {
    x:1,
    y:1
}
```

### ReturnType
```ts
function f() {
  return { x: 10, y: 3 };
}
type P = ReturnType<typeof f>; //type P = { x: number; y: number; }
```


## 函数中的应用

- 基本函数
```ts
// 返回值类型为number
function returnVal01():number {
  return 1
}
// 无返回值
function returnVal02():void {
  console.log('hello')
}
// 定义函数传参类型
function returnVal03(value1:number, value2:string,):string {
  return value1 + value2
}
// 单独定义函数接口 类似于interface
let myFun:(a:number, b: string) => string;
myFun = returnVal03;
```

- 类函数
```ts
// 基本使用
class Person {
  constructor(name:string, public username:string) {
    this.name = name;
    this.username = name;
  }
  // 都可以使用
  public name: string;
  // 被保护 当前类跟继承类可以使用
  protected gender: string = '男';
  // 私有的 当前类可以使用
  private age: number = 27;
  setGender(gender:string) {
    this.gender = gender
  }
}
let person = new Person('Even', 'levi');
person.setGender('上海');
// 类的继承
class Student extends Person {
  constructor(name:string) {
    super(name, name);
  }
}
// class类get\set
class Person2 {
  private _name:string = 'Even'
  constructor() {}
  get getName() {
    return this._name;
  }
  set setName(name:string) {
    this._name = name
  }
}
let person2 = new Person2()
person2.setName = 'levi'
console.log(person2.getName);
```

## 接口
- interface\type区别：`interface可继承， type不可以继承`
### interface
```ts
interface Person1 {
  name:string, // : 必须要写
  age:number,
  gender?:string, // ?可选
  readonly salary:number, // 只读属性
  [propName: string]: any, // 任意类型
  subValue:(val:number) => void,
}
```
### type
```ts
type Person2 = {}
let person:Person1 = {
  name:'Even',
  age: 28,
  salary: 7000,
  ids:[12,12,12],
  id2:[12,12,12],
  id3: 'shi',
  subValue(val) {},
}

```

### declare声明
1. 使用第三方库，使用 declare 关键字来定义它的类型，帮助 TypeScript 判断我们传入的参数类型对不对
```ts
declare var jQuery: (selector: string) => any;
jQuery('#foo');
```

2. 模块扩展
```ts
// 1.ts
export class AClass {
  public a:string;
  constructor(a:string) {
    this.a = a;
  }
 }
// 2.ts
import { AClass } from './1';
// module 后面跟模块名
declare module './1' {
   interface AClass {
    test: (b: number) => number;
  }
}
AClass.prototype.test = (b: number): number => {
   return b;
}
```


### 接口实际应用
```ts
// 接口应用到对象中
type Person2 = {}
let person:Person1 = {
  name:'Even',
  age: 28,
  salary: 7000,
  ids:[12,12,12],
  id2:[12,12,12],
  id3: 'shi',
  subValue(val) {},
}
// 接口应用到class中 implements
interface person3Interface1 {
  id:number,
  type: string,
}
interface person3interface2 {
  name: string,
  age: number,
}
class Person3 implements person3Interface1,person3interface2 {
  public id:number = 12
  public type:string = 'worker'
  public name:string = 'levi'
  public age:number = 28
}
// interface继承
interface person3interface3 extends person3interface2 {
  gender:string
}
const a1:person3interface3 = {
  name: 'Even',
  age: 12,
  gender: 'man'
}
```

## ts中的特殊符号

1. `|`
```ts
// 类似于 ||
interface Iname {
  name: string | number
}
```

2. `??`
```ts
// 类似于 &&
console.log(null || 5)   //5
console.log(null ?? 5)     //5

console.log(undefined || 5)      //5
console.log(undefined ?? 5)      //5

console.log(0 || 5)       //5
console.log(0 ?? 5)      //0
```

3. `?.`
-  a?.b 相当于 a && a.b ? a.b : undefined
```ts
const a = {
      b: { c: 7 }
};
console.log(a?.b?.c);     //7
console.log(a && a.b && a.b.c);    //7
```

4. `!.`
- 断言
```ts
const inputRef = useRef<HTMLEInputlement>(null);
// 定义了输入框，初始化是null，但是你在调用他的时候相取输入框的value，这时候dom实例一定是有值的，所以用断言
const value: string = inputRef.current!.value;
// 这样就不会报错了
```

## Antd接口

- table中`columns`- `ColumnsType`接口

```ts
import { ColumnsType } from 'antd/lib/table'
type IList = {
  id: number,
  title: string
}

let columns:ColumnsType<IList> = useMemo(() => {
  return [
    {title: 'ID', dataIndex: 'id'},
    {title: '任务名称', dataIndex: 'title'}
  ]
})
```

- table中`columns`- `ColumnProps`接口

```ts
import React from 'react';
import { Table, ColumnProps } from 'antd';

// Define the data type for your rows
interface RowData {
  key: string;
  name: string;
  age: number;
  address: string;
}

// Define your columns
const columns: ColumnProps<RowData>[] = [
  {
    title: 'Name',
    dataIndex: 'name',
    key: 'name',
    // You can add additional properties like sorter, render, etc.
  },
  {
    title: 'Age',
    dataIndex: 'age',
    key: 'age',
  },
  {
    title: 'Address',
    dataIndex: 'address',
    key: 'address',
  },
];
```

- table中 `pagination` - `TablePaginationConfig`接口
```ts
import { TablePaginationConfig } from 'antd/es/table';
const handleTableChange = (pagination:TablePaginationConfig, filters, sorter) => {
  const {pageSize, current} = pagination;
  setQueryParams({})
  apiFetch({limit:pageSize, page: current})
};
```

- `useRef` 一般在组件中使用

```ts
// 定义接口
type ILogmodalRef = {
  changeOpen: (newVal:boolean, record:ICollectionList) => void;
}
// 定义变量
const logModalRef = useRef<ILogmodalRef>(null);
//定义方法
const lookLog = useCallback((record: ICollectionList) => {
  logModalRef.current?.changeOpen(true, {opraType: 'look',...record});
}, [])
// 在组件中使用
// <LogModal ref={logModalRef}/>

```
