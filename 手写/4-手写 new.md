## 手写 new

```js
function myNew(Fn, ...args) {
    // 创建一个新的对象，作为Fn的实例对象
    const obj = {}
    // 新对象的__proto__设置为构造函数的原型对象prototype
    obj.__proto__ = Fn.prototype
    // 调用Fn，指定this为obj，参数为args列表
    const result = Fn.call(obj, ...args)
    // 如果Fn构造函数返回的是一个对象，则返回这个对象
    // 如果不是，则返回obj
    return result instanceof Object ? result : obj
}
```

