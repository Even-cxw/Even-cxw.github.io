---
title: requestAnimationFrame
date: 2021-11-26 16:15:24
categories: javascript
tags: [javascript]
comments: false
---

- 如图！当UI给如下雪碧图，需要我们实现`喇叭播放效果，并且可以控制播放暂停`,我们只需要不停切换雪碧图位置来实现动画播放效果。
![](/images/ic_voice.png)

## 铺垫

### 生成精灵图JSON网址
- [地址](https://www.leshylabs.com/apps/sstool/)良心网站，可以将我们雪碧图`尺寸大小、x,y位置`以json的格式返回给我们(如下！！！)
- 是不是很惊喜，我们不需要在管ui要数据，不管给什么图我们只要生成json，然后读取数据值实现动画效果，2秒钟搞定动画。
```javascript
  "ic_voice": [
    {
      "name": "ic_voice_01",
      "x": 0,
      "y": 0,
      "width": 60,
      "height": 60
    },
    {
      "name": "ic_voice_02",
      "x": 0,
      "y": 61,
      "width": 60,
      "height": 60
    },
    {
      "name": "ic_voice_03",
      "x": 61,
      "y": 0,
      "width": 60,
      "height": 60
    }
  ]
```

### requestAnimationFrame
- 大家的第一反应是用`setTimeout`实现每秒切换，但是在移动端部分手机会因为型号原因导致动画`卡屏`，所有用此方法[requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)可以完全避免卡顿问题，此函数的回调函数是在`浏览器下一次重绘之前执行`.
```javascript
window.requestAnimationFrame(function(DOMHighResTimeStamp) {
    // callback methods
});
```
- `浏览器下一次重绘之前执行`此回调函数，默认传递参数[DOMHighResTimeStamp](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp#compat-mobile),那么`DOMHighResTimeStamp`又是个什么鬼? 请继续往下看！！！

### DOMHighResTimeStamp
- [DOMHighResTimeStamp](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp#compat-mobile)翻译成汉语：<font color="red">【dom高精度时间戳】</font>,既然是高精度肯定要比毫秒还要精准（微妙级别), 让我们疑惑的是为什么小数点之前只有5位，甚至可能只有俩位数。他跟获取时间戳不太相同，他是：`自创建上下文以来经过的时间`
> 想要观察 doSomething方法执行了多久？
```javascript
let highTimeStamp1 = window.performance.now() // 21152.700000047684
function doSomething() {
    console.log(111)
}
let highTimeStamp2 = window.performance.now() // 21152.700000047684
// Difference doSomething方法执行所用的时间。
let Difference = highTimeStamp2 - highTimeStamp1;
```
- js里我们一般用最多的是毫秒级别， 1s = 1000毫秒， 秒的时间戳是10位，那么毫秒时间戳便是13位。
```javascript
// 获取当前时间戳
let timeStamp = new Date().getTime(); // 1637891606442
```

<!-- more -->

### 实现requestAnimationFrame循环调用
- 如下代码！fn函数观察发现他只会执行`一次`,`自创建上下文以来经过这里时会执行`
```javascript
window.requestAnimationFrame(function fn(DOMHighResTimeStamp) {
    console.log('我执行了几次')
});
```
- 那么如何循环执行呢？ 当然是函数自调用，执行`fn函数`时，在自己调用自己。
```javascript
window.requestAnimationFrame(function fn(DOMHighResTimeStamp) {
    console.log('我被执行了几次');
    requestAnimationFrame(fn)
});
```

### 代码如下地址
- [github地址](https://github.com/Even-cxw/demo/tree/main/%E9%9B%AA%E7%A2%A7%E5%8A%A8%E7%94%BB%E5%9B%BE)


----

## 变速

### 如何控制变速呢?
> 当我们做九宫格或者其他动画的时候某些`动画需要由快到慢`等执行,直接看代码解析
```javascript
// 循环执行render函数
function render(timestamp) {
  // elapsedTime上一次执行的时间间隔
  const elapsedTime = timestamp - startTime;
  // 如果在speed之外，将执行条件语句里的函数
  if (elapsedTime > speed) {
    startTime = timestamp;
    // 动画执行逻辑
    rotate()
  }
  timer = window.requestAnimationFrame(render);
}
```
- 通过注释的理解我们是不是可以改变`speed`的大小来调节`条件语句`执行频率来达到`变速`效果。
- 至于如何控制speed有很多方法，[简单的demo代码]()大家可以参考下！

### 封装`变速`方法(AnimationFrame)！
> 反向推需求！
#### 参数
1. 需要初始频率，定义为`speed`
2. 需要初始是加速、减速、匀速、定义为`speedFlag` 默认`匀速`
3. countCallback:Function 控制匀速降速函数
4. fun:Function,动画函数，每次循环会执行次函数。
#### 类函数方法
1. 抛出：升速、降速、匀速
2. 抛出：暂停

- 使用 [github地址](https://github.com/Even-cxw/demo/tree/main/%E5%8F%98%E9%80%9F)
```javascript
new AnimationFrame({
  mainFun:function() {console.log('我是主要逻辑函数')},
  countCallback: function(count){
    if (count == 10) {
      console.log('开始加速')
      this.quicken()
    } else if (count == 20) {
      console.log('开始减速')
      this.slow()
    } else if (count == 25) {
      console.log('暂停')
      this.stop();
    }
  }
})
```

- 类函数代码
```javascript
/**
 * @desc AnimationFrame控制快慢类
 * @param {speedFlag|number} 0:定速 1:加速 -1:减速
 * @param {speed|number} 频率
 * @param {countCallback|Function} 变频函数
 * @param {mainFun|Function} 主要业务逻辑函数
 * @param {changeSpeed:numeber} 加速/减速-频率值
*/
// var speedFlag = 1 // 快慢节奏
// var count = 0 // 执行了几个
// var speed = 330 // 动画频率/毫秒 (多少秒执行一次fun)
// var timer = null // 执行id
// var startTime = window.performance.now();

class AnimationFrame {
	constructor({speedFlag=0, speed = 500, changeSpeed = 50, countCallback = () => {}, mainFun = () => {} } = {}) {
		this.speedFlag = speedFlag;
		this.speed = speed;
		this.mainFun = mainFun;
		this.countCallback = countCallback;
		this.timer = null;
		this.startTime = window.performance.now();
		this.count = 0;
		this.changeSpeed = changeSpeed;
		this.isStop = false;
		this.render();
	}
	// 主函数
	render(timestamp) {
		if (this.isStop) return;
    const elapsedTime = timestamp - this.startTime;
    if (elapsedTime > this.speed) {
        this.startTime = timestamp;
        this.animate();
				this.handleSpeed();
    }
    this.timer = window.requestAnimationFrame(this.render.bind(this));
	}
	// 执行动画函数
	animate() {
		this.countCallback(this.count);
		if (this.isStop) return;
		this.mainFun(this.count);
		this.count++
	}
	// 处理频率函数
	handleSpeed() {
		if (this.speedFlag === 1) {
			// 当减到最小值时，自动变匀速；
			this.speed>this.changeSpeed?this.speed -= this.changeSpeed:this.speedFlag = 0;
		} else if (this.speedFlag === -1) {
			this.speed += this.changeSpeed;
		}
	}
	// 加速
	quicken() {
		this.speedFlag = 1
	}
	// 减速
	slow() {
		this.speedFlag = -1
	}
	// 匀速
	equally() {
		this.speedFlag = 0
	}
	// 停止
	stop() {
		cancelAnimationFrame(this.timer);
		this.isStop = true;
	}
}
```