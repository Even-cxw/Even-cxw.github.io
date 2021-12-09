---
title: canvas
date: 2021-12-06 16:15:24
categories: canvas
tags: [canvas]
comments: false
---

> 18年的时候用phase.js框架开发了一年的游戏，其实也是基于canvas的，但是api更接近dom的操作，最近有时间来搞一下canvas。

## 保存状态`save`,`restore`
[代码]()
- 设想：当你给字体设置红色之后，想恢复默认颜色，你是否还需要在设置一次？答案是`否`,可以通过restore来恢复默认值
```javascript
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");
ctx.save(); // 将当前状态放入栈中
// 代码一
ctx.font = "48px serif";
ctx.fillStyle = 'red';
ctx.fillText("Hello world", 50, 50);

ctx.restore() // 恢复到最近的保存状态的方法
ctx.font = "48px serif";
ctx.fillText("Hello world", 50, 100);
```
> 不光可以恢复颜色，translate等位移量也可以恢复。

- 也可以执行俩次`restore`，回归到`上上个状态`；
```javascript
ctx.save(); // 将当前状态放入栈中
ctx.font = "48px serif";
ctx.fillStyle = 'red';
ctx.save(); // 将当前状态放入栈中
ctx.fillText("Hello world", 50, 50);
ctx.restore()
ctx.restore()
ctx.font = "48px serif";
ctx.fillText("Hello world", 50, 100);
```


## 旋转偏移量-基点
[代码]()
> 当我们做旋转动画的时候，肯定要知道是相对于哪一点旋转的，dom是默认自身中心点，而canvas是想对于`原始点`，可以通过translate改变原始点。（官网话术）
- 虽然这么`说`其实在真正的编码中还会碰到很多其他问题，其实canvas编码顺序是很重要的，间接影响效果。
- 代码一先translate在rotate,基点相对于translate的点。
```javascript
ctx.fillStyle = 'red';
ctx.translate(20, 20);
ctx.rotate(20 * Math.PI / 180);
ctx.fillRect(0, 0, 50, 50);
```
> 如图他的基点 - 每次旋转左上角都不会发生位移
![](/images/origin01.png)

- 代码一先rotate在translate,基点相对于rotate之前的点。
```javascript
ctx.restore(); // 恢复到最近的保存状态的方法
ctx.fillStyle = 'green';
ctx.rotate(20 * Math.PI / 180);
ctx.translate(20, 20);
ctx.fillRect(0, 0, 50, 50);
```
> 如图他的基点 - 每次旋转相对于左上角。
![](/images/origin02.png)

<!--more-->

- 参照中心点旋转
```javascript
ctx.restore(); // 恢复到最近的保存状态的方法
ctx.fillStyle = 'green';
ctx.rotate(60 * Math.PI / 180);
ctx.translate(-25, -25); // -25是宽度的一半
ctx.fillRect(0, 0, 50, 50);
```

## 将时间与旋转角度相关联
- 如何实现： `每一秒钟旋转角度+6度！`初始角度为0.  
```javascript
// 1. 获取当前秒数 => new Date().getSeconds()
// 2. 将当前秒数*6 => 6*Math.PI/180
let angle = (6*Math.PI/180)*new Date().getSeconds()
```
> 这样就可以根据当前时间转换角度。 在做canvas的rotate动画时经常使用。


## canvas适配
- 为什么要适配？看到过很多同学这么写达到适配效果。`切记`这样写虽然可以，但是当你操作canvas事件，用到`x`,`y`,坐标的时候会发现`坐标不准确的问题`。
> [一个刮刮乐的demo]()可以为大家用来测试,有俩个`canvas`切换canvas发现设置了`style`的`canvas`坐标点失效
```javascript
<canvas id="canvas" style="width: 100%;height: 100%;"></canvas>
<canvas id="canvas"></canvas>
```
- canvas有自己的`width`, `height`属性是`px`单位，也无法设置成`vw`、`rem`等适配单位。
> 大家第一想法是算屏幕宽高给canvas赋值，[demo代码！！！]()
```javascript
var clientWidth,clientHeight;
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext("2d");
resizeCanvas();
window.addEventListener('resize', function() {
    resizeCanvas();
})
function resizeCanvas() {
    // 获取屏幕宽高
    // 谷歌浏览器 document.documentElement.clientWidth
    // ie浏览器 document.body.clientHeight
    clientWidth = document.body.clientWidth || document.documentElement.clientWidth;
    clientHeight = document.body.clientHeight || document.documentElement.clientHeight;
    canvas.width = clientWidth;
    canvas.height = clientHeight;
    console.log(clientWidth,clientHeight)
}
ctx.fillStyle = 'green';
ctx.fillRect(20, 10, clientWidth/10, clientHeight/10);
```
- 可以从git上下载demo,  刚开始绿色矩形正常渲染，当你改变屏幕宽度执行了`resizeCanvas`方法的时候，发现`矩形消失了`。我们只是改变了宽高。
> 既然`消失`,那我们是不是可以每次随着重置canvas宽高来`重新绘矩形`, [demo代码！！]()
```javascript
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext("2d");
resizeCanvas();
window.addEventListener('resize', function() {
    resizeCanvas();
})
function resizeCanvas() {
    // 获取屏幕宽高
    // 谷歌浏览器 document.documentElement.clientWidth
    // ie浏览器 document.body.clientHeight
    var clientWidth = document.body.clientWidth || document.documentElement.clientWidth;
    var clientHeight = document.body.clientHeight || document.documentElement.clientHeight;
    canvas.width = clientWidth;
    canvas.height = clientHeight;
    console.log(clientWidth,clientHeight)
    renderCanvas(clientWidth,clientHeight)
}
// 从新绘制
function renderCanvas(clientWidth,clientHeight) {
    ctx.fillStyle = 'green';
    ctx.fillRect(20, 10, clientWidth/10, clientHeight/10);
}
```

- canvas铺满整个屏幕会出现失真，所以我们可以设置一个比例如`4：3`展示,剩下的就`留白`或者`留黑`。
> 1. 找到宽高最小的`值`如`iponex => (宽:375, 高：812)`, 找到宽`375/x = 4/3`便可以推出高度x值。
> eg: 如果手机屏幕横过来宽高调换`iponex => (宽:812, 高：375)`，找到宽`x/375 = 4/3`便可以推出宽度x值。
> [gitHub代码demo]()
```css
html,
body {
    height: 100vh;
    width: 100vw;
}
* {
    margin: 0;
    padding: 0;
}
.canvas-wrapper {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
}
```
```html
<div class="canvas-wrapper">
    <canvas id="canvas"></canvas>
</div>
```
```javascript
// js
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext("2d");
resizeCanvas();
window.addEventListener('resize', function () {
    resizeCanvas();
})
function resizeCanvas() {
// 获取屏幕宽高
// 谷歌浏览器 document.documentElement.clientWidth
// ie浏览器 document.body.clientHeight
var clientWidth = document.body.clientWidth || document.documentElement.clientWidth;
var clientHeight = document.body.clientHeight || document.documentElement.clientHeight;
console.log(clientWidth, clientHeight)
// 设计图纸800/600。
var whRate = 800/600;
var cwidth,cheight;pr
if(clientWidth - clientHeight > 0) {
    // 宽大于高， 以高度为基准
    cheight = clientHeight
    cwidth = cheight*whRate
} else {
    cwidth = clientWidth
    cheight = cwidth/whRate
}
canvas.width = cwidth;
canvas.height = cwidth;
ctx.fillStyle = 'black';
ctx.fillRect(0, 0, cwidth, cwidth);
}
```

- 容器适配解决了，但是内容该如何适配呢？我们可以参照其他适配方案定义`方法`,将传递的`x,y,width,height`按照比列转换-达到适配效果！
> [demo代码]()
```javascript
// 将涉及x,y等坐标的加上calcX等方法。
ctx.fillRect(calcX(50), calcY(50), calcX(50), calcY(50));
function calcX(X) {
    return X/designWidth*cwidth
}
function calcY(Y) {
    return Y/designHeight*cheight
}
```