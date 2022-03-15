手写 instanceof

```js
// a instanceof Object
// 判断Object的prototype是否在a的原型链上。
function myInstanceof(obj, Fn) {
    const proto = obj.__proto__

    if (proto) {
        if (Fn.prototype === proto) {
            return true
        } else {
            return myInstanceof(proto, Fn)
        }
    } else {
        return false
    }
}

var arr = [1]
console.log(myInstanceof(arr, Object));
```

