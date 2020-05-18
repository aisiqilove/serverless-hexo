---
title: MVVM整合
date: 2020-05-06 17:22
tags: 
- MVVM
- vue
---

## MVVM.js

``` js
class MVVM {
    constructor(options) {
        // 挂载在实例
        this.$el = options.el;
        this.$data = options.data;
        // 如果有模版就编译
        if (this.$el) {
            // 数据劫持
            new Observer(this.$data);
            // 代理this
            this.proxyData(this.data);
            // 用数据和元素编译
            new Compile(this.$el, this);
        }
    }
    proxyData(data) {
        Object.keys(data).forEach(key => {
            Object.defineProperty(this, key, {
                get() {
                    return data[key];
                },
                set(newValue) {
                    data[key] = newValue;
                }
            })
        })
    }
}
```

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>new MVVM</title>
</head>

<body>
    <div id="app">
        <input type="text" v-model="msg">
        {{msg}}
        <div>
            <ul>
                <li></li>
            </ul>
        </div>
        <!-- {{1231}} -->
    </div>
    <script src="./watcher.js"></script>
    <script src="./observer.js"></script>
    <script src="./compile.js"></script>
    <script src="./MVVM.js"></script>
    <script>
        let vm = new MVVM({
            el: '#app',
            data: {
                msg: 'hello MVVM'
            }
        })
    </script>
</body>

</html>
```

## Compile.js

``` js
class Compile {
    constructor(el, vm) {
        this.el = this.isElementNode(el) ? el : document.querySelector(el);
        this.vm = vm;
        if (this.el) {
            let fragment = this.node2fragment(this.el);
            this.compile(fragment);
            this.el.appendChild(fragment);
        }
    }
    /* 辅助方法 */
    isElementNode(node) {
        return node.nodeType === 1;
    }
    isDirective(name) {
        return name.includes('v-');
    }

    /* 核心方法 */
    node2fragment(el) {
        let fragment = document.createDocumentFragment();
        let firstChild;
        while (firstChild = el.firstChild) {
            fragment.appendChild(firstChild);
        }
        return fragment;
    }
    compileElement(node) {
        let attrs = node.attributes;
        Array.from(attrs).forEach(attr => {
            let attrName = attr.name;
            if (this.isDirective(attrName)) {
                let expr = attr.value;
                let [, type] = attrName.split('-');
                compileUtil[type](node, this.vm, expr);
            }
        })
    }
    compileText(node) {
        // {{}}
        let expr = node.textContent;
        let reg = /\{\{([^}]+)\}\}/g;
        if (reg.test(expr)) {
            compileUtil['text'](node, this.vm, expr);
        }
    }
    compile(fragment) {
        let childNodes = fragment.childNodes;
        Array.from(childNodes).forEach(node => {
            if (this.isElementNode(node)) {
                this.compileElement(node);
                this.compile(node);
            } else {
                this.compileText(node);

            }
        });
    }
}

compileUtil = {
    getVal(vm, expr) {
        expr = expr.split('.'); // [a,b,c,d,e]
        return expr.reduce((prev, next) => {
            return prev[next];
        }, vm.$data)
    },
    getTextVal(vm, expr) {
        return expr.replace(/\{\{([^}]+)\}\}/g, (...args) => {
            return this.getVal(vm, args[1])
        })
    },
    setVal(vm, expr, value) {
        expr = expr.split('.'); // [a,b,c,d,e]
        return expr.reduce((prev, next, currentIndex) => {
            if (currentIndex === expr.length - 1) {
                return prev[next] = value;
            }
            return prev[next];
        }, vm.$data)
    },
    text(node, vm, expr) {
        let updateFn = this.updater['textUpdater'];
        // {{msg.a}} => hello MVVM
        let value = this.getTextVal(vm, expr);
        expr.replace(/\{\{([^}]+)\}\}/g, (...args) => {
            new Watcher(vm, args[1], () => {
                updateFn && updateFn(node, this.getTextVal(vm, expr));
            })
        })
        updateFn && updateFn(node, value);
    },
    model(node, vm, expr) {
        let updateFn = this.updater['modelUpdater'];
        new Watcher(vm, expr, (newValue) => {
            updateFn && updateFn(node, this.getVal(vm, expr));
        })
        node.addEventListener('input', (e) => {
            let newValue = e.target.value;
            this.setVal(vm, expr, newValue);
        })
        updateFn && updateFn(node, this.getVal(vm, expr));
    },
    updater: {
        textUpdater(node, value) {
            node.textContent = value;
        },
        modelUpdater(node, value) {
            node.value = value;
        }
    }
}
```

## Observer.js

``` js
class Observer {
    constructor(data) {
        this.observer(data);
    }
    observer(data) {
        if (!data || typeof data !== 'object') {
            return;
        }
        Object.keys(data).forEach(key => {
            this.defineReactive(data, key, data[key]);
            this.observer(data[key]);
        })
    }
    defineReactive(obj, key, value) {
        let that = this;
        let dep = new Dep(); // 每个变化的数据 都会对应一个数组 这个数组是存放所有更新操作
        Object.defineProperty(obj, key, {
            enumerable: true,
            configurable: true,
            get() {
                Dep.target && dep.addSub(Dep.target)
                return value;
            },
            set(newValue) {
                if (newValue != value) {
                    that.observer(newValue);
                    value = newValue;
                    dep.notify();
                }
            }
        })
    }
}

class Dep {
    constructor() {
        // 订阅数组
        this.subs = [];
    }
    addSub(watcher) {
        this.subs.push(watcher);
    }
    notify() {
        this.subs.forEach(watcher => watcher.update())
    }
}
```

## Watcher.js

``` js
class Watcher {
    constructor(vm, expr, cb) {
        this.vm = vm;
        this.expr = expr;
        this.cb = cb;
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
        let value = this.getVal(this.vm, this.expr);
        Dep.target = null;
        return value;
    }
    update() {
        let newValue = this.getVal(this.vm, this.expr);
        let oldValue = this.value;
        if (newValue != oldValue) {
            this.cb(newValue);
        }
    }
}
```
