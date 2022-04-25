---
title: 滴滴
date: 2022-3-11 16:15:24
categories: js
tags: [js]
comments: false
---

## 重写forEach
- 每次调用forEach执行自定义函数`fun01(){}`


<!--more--> 

## 排序
- 冒泡排序
> 定义： 比较相邻的前后二个数据，如果前面数据大于后面的数据，就将二个 数据交换。
> 这样对数组的第0个数据到N-1个数据进行一次遍历后，最大的一个数据就“沉”到数组第N-1个位置。
> N=N-1，如果N不为0就重复前面二步，否则排序完成。
```javascript
 // 第一组排序
 // [6,1,2,4,3,5]
 // [1,6,2,4,3,5]
 // [1,2,6,4,3,5]
 // [1,2,4,6,3,5]
 // [1,2,4,3,6,5]
 // [1,2,4,3,5,6]
 // 后续还n组
b1.forEach((m,i) => {
    b1.forEach((mm,ii) => {
        if(b1[ii] > b1[ii+1]) {
            var temp = b1[ii];
            b1[ii] = b1[ii+1];
            b1[ii+1] = temp
        }
    })
})
console.log(b1)
```

- 选择排序
> 比如在一个长度为N的无序数组中，在第一趟遍历N个数据，找出其中最小的数值与第一个元素交换，第二趟遍历剩下的N-1个数据，找出其中最小的数值与第二个元素交换……第N-1趟遍历剩下的2个数据，找出其中最小的数值与第N-1个元素交换，至此选择排序完成。
```javascript
function selectSort(arr){
    var min,temp;
    for(var i=0;i<arr.length-1;i++){
        min=i;
        for(var j=i+1;j<arr.length;j++){
            if(arr[j]<arr[min]){
                min = j;
            }
        }
        temp=arr[i];
        arr[i]=arr[min];
        arr[min]=temp;

    }
    return arr;
}
console.log(selectSort([6,1,2,4,3,5])) 
```

- 快速排序
> 1．先从数列中取出一个数作为基准数。
> 2．分区过程，将比这个数大的数全放到它的右边，小于或等于它的数全放到它的左边。
> 3．再对左右区间重复第二步，直到各区间只有一个数。
```javascript
function quickSort(arr){
      if(arr.length<2){return arr}

      var left=[],right=[],mid=arr.splice(Math.floor(arr.length/2),1);

      for(var i=0;i<arr.length;i++){
            if(arr[i]<mid){
                left.push(arr[i]);
            }else {
                right.push(arr[i])
            }
      }
      return quickSort(left).concat(mid,quickSort(right))
  }
 console.log(quickSort([6,1,2,4,3,5]))
```

- 插入排序
> 从第一个元素开始，该元素可以认为已经被排序；
> 取出下一个元素，在已经排序的元素序列中从后向前扫描；
> 如果该元素（已排序）大于新元素，将该元素移到下一位置；
> 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
> 将新元素插入到该位置后；
> 重复步骤2~5。

```javascript
 function insertSort(arr){
    var len = arr.length;
    for (var i = 1; i < len; i++) {
        var key = arr[i];
        var j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
    return arr;
}

```