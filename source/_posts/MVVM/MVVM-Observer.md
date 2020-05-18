---
title: MVVM之数据劫持Observer
date: 2020-05-06 16:56
tags: 
- MVVM
- vue
---

## Observer

MVVM的理论，就不去赘述了。

以Vue中的Observer 数据劫持来说 其主要使用ES5中的Object.defineProperty(vue3.x中表示弃用Object.defineProperty使用更好的原生proxy) 去给对象的属性添加get和set，废话不多说上代码。

Observer.js

``` js
class Observer {
    constructor(data) {
        this.observer(data);
    }
    observer(data) {
        // data 原有属性改成get set
        if (!data || typeof data !== 'object') {
            return;
        }
        // 要将数据 一一劫持 先获取到data的key和value
        Object.keys(data).forEach(key => {
            this.defineReactive(data, key, data[key]);
            this.observer(data[key]); // 深度递归劫持    
        })
    }
    // 定义响应式
    defineReactive(obj, key, value) {
        let that = this;
        Object.defineProperty(obj, key, {
            enumerable: true, // 可枚举
            configurable: true,
            get() {
                return value;
            },
            set(newValue) {
                if (newValue != value) {
                    // this 不是实例
                    that.observer(newValue); // 如果是对象继续劫持
                    value = newValue;
                }
            }
        })
    }
}
```
