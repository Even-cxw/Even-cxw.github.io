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

## 代码如下
- [github地址](https://github.com/Even-cxw/demo/tree/main/%E9%9B%AA%E7%A2%A7%E5%8A%A8%E7%94%BB%E5%9B%BE)
```javascript
 // 生成精灵图JSON网址: https://www.leshylabs.com/apps/sstool/;
  /**
   * @desc 动画函数，移动雪碧图
   * @param {Element} dom 需要插入的dom
   * @param {string} key 雪碧图名称
   * @param {number} speed 动画频率/毫秒
   */
  class AnimationHelp {
    constructor(dom, key, speed) {
      // 记录动画开启/结束状态
      this.isStart = false;
      // 存雪碧图的宽高、x\y坐标点。
      this.imageAttribute = {};
      // 当前时间
      this.startTime = window.performance.now();
      // 当前动画x,y下角标
      this.nowFramesIndex = 0;
      this.dom = dom;
      this.speed = speed;
      this.imageUrl = "./static/" + key + ".png";
      this.getJSONData(key);
    }

    // 得到雪碧图JSON数据
    getJSONData(key) {
      let url = './static/allAnimation.json';
      $.get(url, result => {
        let keyData = result[key];
        this.imageAttribute.height = keyData[0].height;
        this.imageAttribute.width = keyData[0].width;
        this.imageAttribute.childrens = [];
        for (let item of keyData) {
          let value = [];
          value.push(item.x);
          value.push(item.y);
          this.imageAttribute.childrens.push(value);
        }
        console.log('this.imageAttribute', this.imageAttribute);
        this.initSpriteMap();
        this.start();
      });
    }

    // 初始化雪碧图
    initSpriteMap() {
      this.spriteMapDiv = document.createElement('div');
      this.dom.appendChild(this.spriteMapDiv);
      let divCss = {
        height: this.imageAttribute.height + 'px',
        width: this.imageAttribute.width + 'px',
        overflow: 'hidden'
      };
      $(this.spriteMapDiv).css(divCss);
      this.img  = document.createElement('img');
      this.img.src = this.imageUrl
      $(this.spriteMapDiv).append(this.img);
    }

    // 停止动画
    stop() {
      if (!this.isStart) return;
      cancelAnimationFrame(this.animationId);
      let initX = this.imageAttribute.childrens[0][0];
      let initY = this.imageAttribute.childrens[0][1];
      this.img && $(this.img).css("transform", `translate(-${initX}px, -${initY}px)`);
      this.isStart = false;
    }

    // 开始动画
    start() {
      if (this.isStart) return;
      this.isStart = true;
      this.repeatAnimation();
    }

    // 循环开始动画
    repeatAnimation() {
      let that = this;
      that.animationId = requestAnimationFrame(function fn(timestamp) {
        that.rander(timestamp);
        that.animationId = requestAnimationFrame(fn);
      });
    }

    rander(timestamp) {
      const elapsedTime = timestamp - this.startTime;
      if (elapsedTime > this.speed) {
        this.startTime = timestamp;
        if (this.imageAttribute.childrens.length <= this.nowFramesIndex) {
          this.nowFramesIndex = 0;
        }
        let x = this.imageAttribute.childrens[this.nowFramesIndex][0];
        let y = this.imageAttribute.childrens[this.nowFramesIndex][1];
        $(this.img).css("transform", `translate(-${x}px, -${y}px)`);
        this.nowFramesIndex++;
      }
    }
  }

```