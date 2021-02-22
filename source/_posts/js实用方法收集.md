---
title: js实用方法收集
date: 2021-02-22 11:29:02
tags:
---


### 时间格式化

```javascript

const formatTime = date => {
  const year = date.getFullYear()
  const month = date.getMonth() + 1
  const day = date.getDate()
  const hour = date.getHours()
  const minute = date.getMinutes()
  const second = date.getSeconds()
  return [year, month, day].map(formatNumber).join('/') + ' ' + [hour, minute, second].map(formatNumber).join(':')
}

```



### 根据下标删除数组元素

```javascript

const removeItem = (arr, index) => {
    arr.splice(index, 1)
}

```


### 从数组中删除指定的一个元素

```javascript

const removeItem = (arr, val) => {
    var index = arr.indexOf(val);
    if (index > -1) {
        arr.splice(index, 1);
    }
}


```


### 从数组中删除指定的多个元素

```javascript

const arrRemoves = (arr, val) => {
    var newIndexs = val.map(function(val, idx){return val - idx})
    newIndexs.forEach(function(index){
        arr.splice(index, 1)
    })
}

```

### 根据数组对象元素id去重

``` javascript
function uniarr (arr) {
    var hash = [];
    for (var i = 0; i < arr.length; i++) {
      for (var j = i + 1; j < arr.length; j++) {
        if (arr[i].id == arr[j].id) {
          ++i;
        }
      }
      hash.push(arr[i]);
    }
    return hash;
}    

```


### 数组根据对象属性排序

```javascript

var arr=[{name:"rose", id:3},{name:"jack", id:1},{name:"mafy",id:2}]
function rule(key) {
    return function (a, b) {
        return a[key] - b[key]
    }
}        
arr.sort(rule("id"))

```


### 随机打乱数组顺序

```javascript

var arr = [1, 2, 3, 4, 5, 6];
function randomsort(a, b) {
    return Math.random() > .5 ? -1 : 1
}
arr.sort(randomsort)

```


### 手机号码加*号


```javascript 
   
var phone = 13834236433;
function addStar(phone) {
    return phone.substr(0, 3) + '****' + phone.substr(-4, 4);
}
addStar(phone);   

```

### 重复字符串N次

```javascript 

var times = function(str, num) {
    return num > 1 ? str += times(str, --num) : str;
}    

```