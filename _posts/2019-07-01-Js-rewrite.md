---
layout: post
title: "Js数组方法重写"
subtitle: "JS Pop,Unshift,Slice,Splice方法重写"
date: 2019-07-01 12:00:00
author: "Li"
header-img: "img/post-bg-js-version.jpg"
header-mask: 0.4
tags:
  - JS
  - slice
  - unshift
  - pop
  - splice
---


---

最近重写了js中Array数组的一些方法，闲着把这些po在下面

pop方法
```javascript
    Array.prototype.popp = function () {
    if (this.length === 0) {
        return undefined
    }
    var b = this[this.length - 1]
    this.length = this.length - 1
    return b
    }
```
ubshift方法
```javascript
    Array.prototype.unshiftt = function () {
    for (let i = arguments.length + this.length - 1; i >= 0; i--) {
        if (i - arguments.length >= 0) {
            this[i] = this[i - arguments.length]
        }
        else {
            this[i] = arguments[i]
        }
    }
    return this.length
    }
```
slice方法
```javascript
    Array.prototype.sliceee = function (start, end) {
    var b = [];
    for (let i = start, j = 0; i < end; i++ , j++) {
        const element = this[i];
        b[j] = this[i]
    }
    return b;
    }
```
splice方法
```javascript
    Array.prototype.spliceee = function (idx, num) {
    var b = []
    if (idx < 0) {
        idx = 0;
    }
    if (idx >= this.length) {
        idx = this.length
    }
    if (num > this.length - idx) {
        num = 0;
    }
    if (num != 0) {
        for (let i = idx, j = 0; i < this.length; i++ , j++) {
            if (b.length < num) {
                b[j] = this[i]
            }
            this[i] = this[i + num];
        }
        this.length = this.length - num;
        if (arguments.length > 2) {
            for (let i = this.length + num; i - num > idx; i--) {
                this[i - 1] = this[i - num - 1]
            }
            for (let i = idx, j = 2; j < arguments.length; i++ , j++) {
                this[i] = arguments[j]
            }
            return this
        }
        return b
    }
    if (num == 0) {
        for (let i = 0; i < this.length - idx; i++) {
            b[i] = this[i + idx];
        }
        this.length = idx;
        if (arguments.length > 2) {
            for (let i = this.length + num; i - num > idx; i--) {
                this[i - 1] = this[i - num - 1]
            }
            for (let i = idx, j = 2; j < arguments.length; i++ , j++) {
                this[i] = arguments[j]
            }
            return this
        }
        return b
    }
    }
```
splice方法没有做简化，第一次重写时，第2个参数是作为选择插入数组的元素个数的，最终还是用for循环来实现的，如果想要精简的话应该可以用几个回调函数。