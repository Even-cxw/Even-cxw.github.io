---
title: 虚拟DOM和diff算法
date: 2021-01-25 16:15:24
categories: vue
tags: [vue]
comments: true
---


## snabbdom介绍
- snabbdom是虚拟dom库，是diff算法的鼻祖，vue源码借鉴了snabbdom; 
> [官方git-ts版本](https://github.com/snabbdom/snabbdom)
> [snabbdom-study尝试使用](https://github.com/Even8/vue-study/tree/main/vue-origin-study/snabbodm-study) 
```javascript
  npm i -D snabbdom
``` 

### 手写Vue源码
- [手写h函数代码](https://github.com/Even8/vue-study/blob/main/vue-origin-study/write-snabbdom/snabbdom/h.js)
```javascript
  // 可以尝试写三种形态分别输出以下虚拟节点
  // 形态① h('div', {}, '文字')
  // 形态② h('div', {}, [])
  // 形态③ h('div', {}, h())
  // 输出对应虚拟节点形态
  let vnode = {
    sel: "div",, // 标签名
    data: {},  // 元素属性
    children: [{sel: 'a',
        data:{},
        text: '1',
        key: undifined,
        elm: undifined,
      }], 
    text: "我是一个盒子" // 文本内容
    key:undifined, // 唯一标识
    elm: undifined, // dom
  }
  ```
  ```javascript
  function h(sel, data, c) {
    // 检查参数的个数
    if (arguments.length != 3)
        throw new Error('对不起，h函数必须传入3个参数，我们是低配版h函数');
    // 检查参数c的类型
    if (typeof c == 'string' || typeof c == 'number') {
        // 说明现在调用h函数是形态①
        return vnode(sel, data, undefined, c, undefined);
    } else if (Array.isArray(c)) {
        // 说明现在调用h函数是形态②
        let children = [];
        // 遍历c，收集children
        for (let i = 0; i < c.length; i++) {
            // 检查c[i]必须是一个对象，如果不满足
            if (!(typeof c[i] == 'object' && c[i].hasOwnProperty('sel')))
                throw new Error('传入的数组参数中有项不是h函数');
            // 这里不用执行c[i]，因为你的测试语句中已经有了执行
            // 此时只需要收集好就可以了
            children.push(c[i]);
        }
        // 循环结束了，就说明children收集完毕了，此时可以返回虚拟节点了，它有children属性的
        return vnode(sel, data, children, undefined, undefined);
    } else if (typeof c == 'object' && c.hasOwnProperty('sel')) {
        // 说明现在调用h函数是形态③
        // 即，传入的c是唯一的children。不用执行c，因为测试语句中已经执行了c。
        let children = [c];
        return vnode(sel, data, children, undefined, undefined);
    } else {
        throw new Error('传入的第三个参数类型不对');
    }
  };
```

### 手写diff算法
- [封装patch函数](https://github.com/Even8/vue-study/blob/main/vue-origin-study/write-snabbdom/snabbdom/patch.js)
```javascript
// key是节点唯一标识，告诉diff算法是否同一个dom节点 ```###如果父节点不是同一个节点，无论key变没变，都会暴力拆除所有子元素```
/**
 * @desc 实现diff算法比较，并将新的dom挂载dom树上；
  * @params {oldVnode:(object|Elemnt),newVnode:object}
  * @oldVnode : 老虚节点或element
  * @newVnode ：新的虚拟节点
*/
function patch(oldVnode, newVnode) {
  // 判断传入的第一个参数，是DOM节点还是虚拟节点？
  if (oldVnode.sel == '' || oldVnode.sel == undefined) {
      // 传入的第一个参数是DOM节点，此时要包装为虚拟节点
      oldVnode = vnode(oldVnode.tagName.toLowerCase(), {}, [], undefined, oldVnode);
  }

  // 判断oldVnode和newVnode是不是同一个节点
  if (oldVnode.key == newVnode.key && oldVnode.sel == newVnode.sel) {
      console.log('是同一个节点');
      patchVnode(oldVnode, newVnode);
  } else {
      console.log('不是同一个节点，暴力插入新的，删除旧的');
      let newVnodeElm = createElement(newVnode);
      
      // 插入到老节点之前
      if (oldVnode.elm.parentNode && newVnodeElm) {
          oldVnode.elm.parentNode.insertBefore(newVnodeElm, oldVnode.elm);
      }
      // 删除老节点
      oldVnode.elm.parentNode.removeChild(oldVnode.elm);
  }
};
```

### 封装createElement函数
- [createElement函数](https://github.com/Even8/vue-study/blob/main/vue-origin-study/write-snabbdom/snabbdom/createElement.js)
```javascript
/**
 * @desc 创建节点
  * @params {vnode:{object}}
  * @vnode ： 虚拟节点
  * @return : Element ： 虚拟节点所有dom树；
*/
function createElement(vnode) {
  // console.log('目的是把虚拟节点', vnode, '真正变为DOM');
  // 创建一个DOM节点，这个节点现在还是孤儿节点
  let domNode = document.createElement(vnode.sel);
  // 有子节点还是有文本？？
  if (vnode.text != '' && (vnode.children == undefined || vnode.children.length == 0)) {
      // 它内部是文字
      domNode.innerText = vnode.text;
  } else if (Array.isArray(vnode.children) && vnode.children.length > 0) {
      // 它内部是子节点，就要递归创建节点
      for (let i = 0; i < vnode.children.length; i++) {
          // 得到当前这个children
          let ch = vnode.children[i];
          // 创建出它的DOM，一旦调用createElement意味着：创建出DOM了，并且它的elm属性指向了创建出的DOM，但是还没有上树，是一个孤儿节点。
          let chDOM = createElement(ch);
          // 上树
          domNode.appendChild(chDOM);
      }
  }
  // 补充elm属性
  vnode.elm = domNode;
  // 返回elm，elm属性是一个纯DOM对象
  return vnode.elm;
};
```

### 封装patchVnode
- [patchVnode函数](https://github.com/Even8/vue-study/blob/main/vue-origin-study/write-snabbdom/snabbdom/atchVnode.js)
```javascript
function patchVnode(oldVnode, newVnode) {
  // 判断新旧vnode是否是同一个对象
  if (oldVnode === newVnode) return;
  // 判断新vnode有没有text属性
  if (newVnode.text != undefined && (newVnode.children == undefined || newVnode.children.length == 0)) {
      // 新vnode有text属性
      console.log('新vnode有text属性');
      if (newVnode.text != oldVnode.text) {
          // 如果新虚拟节点中的text和老的虚拟节点的text不同，那么直接让新的text写入老的elm中即可。如果老的elm中是children，那么也会立即消失掉。
          oldVnode.elm.innerText = newVnode.text;
      }
  } else {
      // 新vnode没有text属性，有children
      console.log('新vnode没有text属性');
      // 判断老的有没有children
      if (oldVnode.children != undefined && oldVnode.children.length > 0) {
          // 老的有children，新的也有children，此时就是最复杂的情况。
          updateChildren(oldVnode.elm, oldVnode.children, newVnode.children);
      } else {
          // 老的没有children，新的有children
          // 清空老的节点的内容
          oldVnode.elm.innerHTML = '';
          // 遍历新的vnode的子节点，创建DOM，上树
          for (let i = 0; i < newVnode.children.length; i++) {
              let dom = createElement(newVnode.children[i]);
              oldVnode.elm.appendChild(dom);
          }
      }
  }
}
```


## vue-cli3配置文件
- webpack的位置文件是新建`webpack.config.js`
- vue的配置文件是新建`vue.config.js`
> 1. `publicPath`会将此参数，传递到`process.env.BASE_URL`
> 2. 创建 .env文件设置变量名`VUE_APP_{变量名}`， 也会传递到`process.env.VUE_APP_{变量名}`
```javascript
module.exports = {
  publicPath: '/subapp/sub-vue',
  transpileDependencies: ['common'],
  chainWebpack: config => config.resolve.symlinks(false),
  configureWebpack: {
    output: {
      // 把子应用打包成 umd 库格式
      library: `${name}-[name]`,
      libraryTarget: 'umd',
      jsonpFunction: `webpackJsonp_${name}`
    }
  },
  devServer: {
    port: process.env.VUE_APP_PORT,
    headers: {
      'Access-Control-Allow-Origin': '*'
    }
  }
}
```

- [封装updateChildren函数](https://github.com/Even8/vue-study/blob/main/vue-origin-study/write-snabbdom/snabbdom/updateChildren.js)
  - 四种命中查找 - 四个指针；循环条件:新前小于新后，旧前小于旧后。
    1. 新前与旧前
    2. 新后与旧后
    3. 新后与旧前
    4. 新前与旧后
  - 如果都没有命中需要循环寻找
```javascript
function updateChildren(parentElm, oldCh, newCh) {
  console.log('我是updateChildren');
  console.log(oldCh, newCh);

  // 旧前
  let oldStartIdx = 0;
  // 新前
  let newStartIdx = 0;
  // 旧后
  let oldEndIdx = oldCh.length - 1;
  // 新后
  let newEndIdx = newCh.length - 1;
  // 旧前节点
  let oldStartVnode = oldCh[0];
  // 旧后节点
  let oldEndVnode = oldCh[oldEndIdx];
  // 新前节点
  let newStartVnode = newCh[0];
  // 新后节点
  let newEndVnode = newCh[newEndIdx];

  let keyMap = null;

  // 开始大while了
  while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
      console.log('★');
      // 首先不是判断①②③④命中，而是要略过已经加undefined标记的东西
      if (oldStartVnode == null || oldCh[oldStartIdx] == undefined) {
          oldStartVnode = oldCh[++oldStartIdx];
      } else if (oldEndVnode == null || oldCh[oldEndIdx] == undefined) {
          oldEndVnode = oldCh[--oldEndIdx];
      } else if (newStartVnode == null || newCh[newStartIdx] == undefined) {
          newStartVnode = newCh[++newStartIdx];
      } else if (newEndVnode == null || newCh[newEndIdx] == undefined) {
          newEndVnode = newCh[--newEndIdx];
      } else if (checkSameVnode(oldStartVnode, newStartVnode)) {
          // 新前和旧前
          console.log('①新前和旧前命中');
          patchVnode(oldStartVnode, newStartVnode);
          oldStartVnode = oldCh[++oldStartIdx];
          newStartVnode = newCh[++newStartIdx];
      } else if (checkSameVnode(oldEndVnode, newEndVnode)) {
          // 新后和旧后
          console.log('②新后和旧后命中');
          patchVnode(oldEndVnode, newEndVnode);
          oldEndVnode = oldCh[--oldEndIdx];
          newEndVnode = newCh[--newEndIdx];
      } else if (checkSameVnode(oldStartVnode, newEndVnode)) {
          // 新后和旧前
          console.log('③新后和旧前命中');
          patchVnode(oldStartVnode, newEndVnode);
          // 当③新后与旧前命中的时候，此时要移动节点。移动新前指向的这个节点到老节点的旧后的后面
          // 如何移动节点？？只要你插入一个已经在DOM树上的节点，它就会被移动
          parentElm.insertBefore(oldStartVnode.elm, oldEndVnode.elm.nextSibling);
          oldStartVnode = oldCh[++oldStartIdx];
          newEndVnode = newCh[--newEndIdx];
      } else if (checkSameVnode(oldEndVnode, newStartVnode)) {
          // 新前和旧后
          console.log('④新前和旧后命中');
          patchVnode(oldEndVnode, newStartVnode);
          // 当④新前和旧后命中的时候，此时要移动节点。移动新前指向的这个节点到老节点的旧前的前面
          parentElm.insertBefore(oldEndVnode.elm, oldStartVnode.elm);
          // 如何移动节点？？只要你插入一个已经在DOM树上的节点，它就会被移动
          oldEndVnode = oldCh[--oldEndIdx];
          newStartVnode = newCh[++newStartIdx];
      } else {
          // 四种命中都没有命中
          // 制作keyMap一个映射对象，这样就不用每次都遍历老对象了。
          if (!keyMap) {
              keyMap = {};
              // 从oldStartIdx开始，到oldEndIdx结束，创建keyMap映射对象
              for (let i = oldStartIdx; i <= oldEndIdx; i++) {
                  const key = oldCh[i].key;
                  if (key != undefined) {
                      keyMap[key] = i;
                  }
              }
          }
          console.log(keyMap);
          // 寻找当前这项（newStartIdx）这项在keyMap中的映射的位置序号
          const idxInOld = keyMap[newStartVnode.key];
          console.log(idxInOld);
          if (idxInOld == undefined) {
              // 判断，如果idxInOld是undefined表示它是全新的项
              // 被加入的项（就是newStartVnode这项)现不是真正的DOM节点
              parentElm.insertBefore(createElement(newStartVnode), oldStartVnode.elm);
          } else {
              // 如果不是undefined，不是全新的项，而是要移动
              const elmToMove = oldCh[idxInOld];
              patchVnode(elmToMove, newStartVnode);
              // 把这项设置为undefined，表示我已经处理完这项了
              oldCh[idxInOld] = undefined;
              // 移动，调用insertBefore也可以实现移动。
              parentElm.insertBefore(elmToMove.elm, oldStartVnode.elm);
          }
          // 指针下移，只移动新的头
          newStartVnode = newCh[++newStartIdx];
      }
  }

  // 继续看看有没有剩余的。循环结束了start还是比old小
  if (newStartIdx <= newEndIdx) {
      console.log('new还有剩余节点没有处理，要加项。要把所有剩余的节点，都要插入到oldStartIdx之前');
      // 遍历新的newCh，添加到老的没有处理的之前
      for (let i = newStartIdx; i <= newEndIdx; i++) {
          // insertBefore方法可以自动识别null，如果是null就会自动排到队尾去。和appendChild是一致了。
          // newCh[i]现在还没有真正的DOM，所以要调用createElement()函数变为DOM
          parentElm.insertBefore(createElement(newCh[i]), oldCh[oldStartIdx].elm);
      }
  } else if (oldStartIdx <= oldEndIdx) {
      console.log('old还有剩余节点没有处理，要删除项');
      // 批量删除oldStart和oldEnd指针之间的项
      for (let i = oldStartIdx; i <= oldEndIdx; i++) {
          if (oldCh[i]) {
              parentElm.removeChild(oldCh[i].elm);
          }
      }
  }
};
```



## 添加element-ui
- 执行命令`vue add element`
- 有俩个选项 1.全局引入 2.按需引入
```
Fully import
Import on demand
```
- 我选择的是第二种按需引入，会`自动修改我们的配置文件`
```javascript
1. 多个plugins文件
2. bael.config.js文件修改
3. mian.js自动引入
```

<!--more-->