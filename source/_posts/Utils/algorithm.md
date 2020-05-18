---
title: 前端简单算法
date: 2019-11-05 09:42
tags: Algorithm
---

## throttle节流

throttle 的概念理解起来更容易，就是固定函数执行的速率，即所谓的“节流”。正常情况下，mousemove 的监听函数可能会每 20ms（假设）执行一次，如果设置 200ms 的“节流”，那么它就会每 200ms 执行一次。比如在 1s 的时间段内，正常的监听函数可能会执行 50（1000/20） 次，“节流” 200ms 后则会执行 5（1000/200） 次。
与 debounce 类似，我们这个 throttle 也接收两个参数，一个实际要执行的函数 fn，一个执行间隔阈值 threshhold。同样的，throttle 的更完整实现可以参看 underscore 的 _.throttle。

``` js
/**
 * @param fn {Function}   实际要执行的函数
 * @param delay {Number}  执行间隔，单位是毫秒（ms）
 * @return {Function}     返回一个“节流”函数
 */
function throttle(fn, delay = 2000) {
    let lock = false
    return (...agrs) => {
        if (lock) {
            return
        }
        fn(...agrs)
        lock = true;
        setTimeout(() => {
            lock = false
        }, delay)
    }
}
```

## debounce防抖

连续操作停止后只执行一次

``` js
/**
 * @param fn {Function}   实际要执行的函数
 * @param delay {Number}  延迟时间，也就是阈值，单位是毫秒（ms）
 * @return {Function}     返回一个函数
 */
function debounce(fn, delay = 2000, i = null) {
    return (...agrs) => {
        clearTimeout(i);
        i = setTimeout((...agrs) => fn(...agrs), delay)
    }
}
```

debounce 返回了一个闭包，这个闭包依然会被连续频繁地调用，但是在闭包内部，却限制了原始函数 fn 的执行，强制 fn 只在连续操作停止后只执行一次。debounce 的使用方式如下

``` js
$(document).on(
    'mouvemove',
    debounce(function(e) {
        console.log('mouvemove')
    }, 500)
);
```

## 数组拉平

### 二维数组拉平

``` js
function flatten(arr) {
    return [].concat(...arr)
}
```

### 多维数组拉平

``` js
function flatten(arr) {
    return [].concat(
        ...arr.map(x =>
            Array.isArray(x) ? flatten(x) : x
        )
    )
}
```

### ES6新方法flat()

``` js
[].flat(Infinity);
```

## 柯里化

柯里化是指这样一个函数(假设叫做createCurry)，他接收函数A作为参数，运行后能够返回一个新的函数。并且这个新的函数能够处理函数A的剩余参数。

``` js
const curry = func => {
    const g = (...allAgrs) => allAgrs.length >= func.length ?
        func(...allAgrs) : (...agrs) => g(...allAgrs, ...agrs);
    return g;
}

const foo = curry((a, b, c, d) => {
    console.log(a, b, c, d)
})

foo(1)(2)(3)(4)
```
