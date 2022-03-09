## 复习
### 2022-02-28
1. loader的常规配置
2. ts class类函数如何定义后置变量
- 单词； 工具、私有

### 2022-03-01
1. 什么是元组。
2. 什么是断言。
3. 定义`对象数组`，对象中有属性为函数，但函数名称未确定。
4. 定义函数类型的俩种写法
5. 如何给window全局加变量

### 2022-03-02
1. webpack 1.hash  2.chunkhash  3.contenthash 有哪些区别？

### 2022-03-03
1. 如何在node环境执行linux命令
2. 返回 Node.js 进程的当前工作目录
- (过程、进程) （创造）(继承) (表示) (地球仪) (全局化)

### 2022-03-04
1. 如何删除非空目录 
2. mode:production对代码做了哪些改变, 目前有三点

### 2022-03-07
1. reduce如何使用



## 答:

### 2022-03-01
1. 什么是元组:已知元素数量和类型的数组
2. 什么是断言
```typescript
let arr: { [key: string]: () => void }[] = [];
arr.push({ a: function () {} });
arr[0].a();
```
4. 定义函数的俩种写法
```typescript
let fun01 = function():void{};
let fun02:() => void = function(){};
```

### 2022-03-03
1. 如何在node环境执行linux命令
2. 返回 Node.js 进程的当前工作目录
```javascript
process.cwd();
```
- (过程、进程)=process （创造)=spawn (继承)=inherit (表示)=express
- (地球仪)=glob (全局化)=globle

### 2022-03-04
1. 如何删除非空目录
```javascript
// rm -rf ./dist
// rimraf包
// clean-webpack-plugin
```