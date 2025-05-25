---
title: 前端计算精度之decimal.js
date: 2025-05-22 19:15:44
tags:
---

在上一个美国股票期权项目中涉及到前端计算小数失去精度的问题，也找了一些相关的解决方案，最终确定的方案还是 decimal 库，下面记录一下安装以及使用，方便下次忘记时查阅。

安装:

```JavaScript

    npm install decimal.js

```

使用以及语法

```JavaScript

// 引入
import { Decimal } from 'decimal.js'

// 创建 Decimal 实例
const a = new Decimal('0.1'); // 使用字符串初始化以确保精度
const b = new Decimal('0.2');

// 加法
const resultAdd = a.add(b);  // 也可以使用 plus
console.log(resultAdd.toString()); // 输出：0.3

// 减法
const resultSub = a.sub(b); // 对应minus
console.log(resultSub.toString()); // 输出：-0.1

// 乘法
const resultMul = a.mul(b); // 对应times
console.log(resultMul.toString()); // 输出：0.02

// 除法
const resultDiv = a.div(b); // 对应dividedBy
console.log(resultDiv.toString()); // 输出：0.5

// 比较大小
const comparison = a.comparedTo(b);
console.log(comparison); // 输出：-1 (a < b)

// 取余
const resultMod = a.modulo(b);
console.log(resultMod.toString()); // 输出：0.1

// 指数运算
const resultPow = a.pow(2);
console.log(resultPow.toString()); // 输出：0.01
s
// 保留小数位数
const resultFixed = a.toFixed(2);
console.log(resultFixed); // 输出：0.10

// 连续运算
const result = new Decimal(0.1)
    .add(0.2)    // 加法
    .sub(0.15)    // 减法
    .mul(2)     // 乘法
    .div(3) // 除法
    .toFixed(2);  // 保留两位小数

```

1.使用字符串初始化: 为了确保精度，最好使用字符串来初始化 Decimal 实例，而不是直接传递浮点数值。例如，使用 new Decimal(‘0.1’) 而不是 new Decimal(0.1)。

2.避免混合类型: 尽量避免在同一计算中混合使用 Decimal 实例和普通的 JavaScript 数字。这可能会导致意外的结果或精度损失。

3.注意精度丢失: 尽管 decimal.js 提供了高精度计算，但在某些情况下，仍可能存在精度丢失。例如，某些数学运算可能会导致无限循环小数，或者在处理非常大的数或非常小的数时，可能会丢失精度。
