---
title: MVVM之观察者Watcher
date:  2020-05-06 17:16
tags: 
- MVVM
- vue
---

## Watcher

通过新值和老值进行对比 如果发生变化 就调用更新方法

Watcher.js
``` js 
class Watcher {

    constructor(vm, expr, cb) {
        this.vm = vm;
        this.expr = expr;
        this.cb = cb;
        // 获取一下老值
        this.value = this.get();
    }
    getVal(vm, expr) {
        expr = expr.split('.');
        return expr.reduce((prev, next) => {
            return prev[next];
        }, vm.$data)
    }
    get() {
        Dep.target = this;
        let value = this.getVal(this.vm,this.expr);
        Dep.target = null;
        return value;
    }
    update() {
        // 对外暴露的方法
        let newValue = this.getVal(this.vm,this.expr);
        let oldValue = this.value;
        if(newValue !=oldValue) {
            this.cb(newValue);
        }
    }

}

```
