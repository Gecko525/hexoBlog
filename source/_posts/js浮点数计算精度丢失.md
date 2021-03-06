---
title: js浮点数计算精度丢失
date: 2020-04-05 21:32:41
tags: js
typora-root-url: ..
---

### 前言

js在计算浮点数时会出现精度误差问题，典型场景是计算金额。

<!--more-->

<img src="/images/js浮点数计算精度丢失/lizi.png" alt="lizi" style="zoom:50%;" />

测试实例：

```js
console.log(0.1+0.2); //0.30000000000000004
```

对于浮点数的四则运算（加减乘除），几乎所有的编程语言都会出现上面类似的精度误差问题，只是大部分语言都处理封装了避免误差的方法。对于js而言，由于它是一门弱类型的语言，所以并没有对浮点数的运算有解决的封装方法，这能我们自己来解决

### 精度丢失原理

计算机在运算的时候会先将10进制数字转换为二进制，计算完成后再转成10进制。

我们先把0.1和0.2转换为二进制：

```js
0.1 => 0.0001 1001 1001 1001..（无限循环）

0.2 => 0.0011 0011 0011 0011…（无限循环）
```

这里可以看出转换为二进制是一个无限循环的数字，**单在计算机中对于无限循环的数字会进行舍入处理的，进行双精度浮点数的小数部分最多支持52位。然后把两个二进制的数进行运算得出的也是一个二进制数值，最后再把它转换为十进制**。保留17位小数，所以0.1+0.2的值就成了 0.30000000000000004。 0.1+0.1的值成了0.20000000000000000，全是0的时候可以省略，就成了0.2。

### 解决方法

#### toFixed方法

最简单的处理，通过`toFixed`方法。

```js
console.log(parseFloat((0.1+0.2).toFixed(1))); //0.3
```

`toFixed`方法可以传入保留小数的位数，只适合所有计算数和结果都保持相同小数位数的运算。

#### Math.round方法

```js
Math.formatFloat = function (f, digit) {
    var m = Math.pow(10, digit);
    return Math.round(f * m, 10) / m;
}
var s = 0.1 + 0.2;
console.log(Math.formatFloat(s, 1)) // 0.3
```

#### 整数转换方法

原理是先将小数转换成整数，计算完成后再转换成小数。加减乘除都可以，下面以加法为例。

```js
function accAdd(arg1, arg2) {
    var r1, r2, m;
    try {
        r1 = arg1.toString().split(".")[1].length;
    } catch(e) {
        r1 = 0;
    }
    try {
        r2 = arg2.toString().split(".")[1].length;
    }catch(e){
        r2 = 0;
    }
    m = Math.pow(10, Math.max(r1, r2));
    return (arg1 * m + arg2 * m) / m;
}
console.log(accAdd(0.1, 0.2)) // 0.3
```

