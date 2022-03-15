## 手写promise

```js
(function(window) {
    /**
     * Promise 构造函数
     * excutor：执行器函数，同步执行
     */
    const PENDING = "pending"
    const RESOLVED = "resolved"
    const REJECTED = "rejected"
    function Promise (excutor) {
        const self = this
        self.status = PENDING
        self.data = undefined
        self.callbacks = []

        // 定义 resolve 和 reject
        function resolve (value) {
            self.status = RESOLED
            self.data = value
            if (self.callbacks.length) {
                setTimeout(() => {
                    self.callbacks.forEach((callbacksObj) => {
                        callbacksObj.onResolved(value)
                    })
                });
            }    
        }

        function reject (reason) {
            // 优化：如果状态不是pending状态，直接退出
            if (self.status !== PENDING) return

            // 状态改变为rejected
            self.status = REJECTED
            // 数据改变为reason
            self.data = reason
            // 如果callbacks数组里有已经指定的回调函数，立即异步执行
            // 为什么要异步执行呢？因为.then指定的回调函数就是要异步执行的
            if (self.callbacks.length > 0) {
                setTimeout(() => {
                    self.callbacks.forEach((callbacksObj) => {
                        callbacksObj.onRejected(reason)
                    })
                });
            }
        }

        try {
            excutor(resolve, reject)
        } catch(error) {
            // 如果报错了，直接返回一个状态为rejected的promise对象
            reject(error)
        }
    }

    /**
     * Promise 原型对象的 then 方法
     * onResolved：成功的回调函数
     * onRejected：失败的回调函数
     * 返回一个新的 promise 对象
     * 返回的 promise 的结果由回调函数的执行结果决定
     */
    Promise.prototype.then = function (onResolved, onRejected) {
        // 简单版本
        const self = this
        this.callbacks.push({onResolved, onRejected})
    }

    /**
     * Promise 原型对象的 catch 方法
     * onRejected：失败的回调函数
     * 返回一个新的 promise 对象
     */
    Promise.prototype.catch = function (onRejected) {
        const self = this
        this.callbacks.push({onResolved: undefined, onRejected})
    }

    /**
     * Promise 对象的 resolve 方法
     * 返回一个指定结果的成功的 promise
     */
    Promise.resolve = function (value) {
        // 返回一个成功/失败的promise
        return new Promise((resolve, reject) => {
            // 如果value是promise
            if (value instanceof Promise) {
                // Promise.resolve返回的promise状态由value的状态决定
                //     如果value成功，则Promise.resolve的状态为成功
                //     如果value失败，则Promise.reject的状态为失败
                value.then(
                    (value) => {resolve(value)},
                    (reason) => {reject(reason)}
                )
            } else {
                // value为非promise，直接返回成功的promise
                resolve(value)
            }
        })
    }

    /**
     * Promise 对象的 reject 方法
     * 返回一个指定结果的失败的 promise
     */
    Promise.reject = function (reason) {
        return new Promise((resolve, reject) => {
            // 直接返回失败的promise
            reject(reason)
        })
    }

    /**
     * Promise 对象的 all 方法
     * 返回一个 promise 对象，只有当全部 promise 都成功时才成功，只要有一个失败则为失败
     */
    Promise.all = function (promises) {
        return new Promise((resolve, reject) => {
            // 定义存储成功结果的数组
            const values = new Array(promises.length)
            // 定义成功promise的计数器
            let resolvedCount = 0
            // 遍历promise获取结果
            promises.forEach((p, index) => {
                Promise.resolve(p).then(
                    value => {
                        resolvedCount++

                        values[index] = value

                        if (resolvedCount === promises.length) {
                            // 全部成功promise结果数组
                            resolve(values)
                        }
                    },
                    reason => {
                        reject(reason)
                    }
                )
            })
        })
    }

    /**
     * Promise 对象的 resolve 方法
     * 返回一个 promise 对象，其结果由第一个完成的 promise 决定
     */
    Promise.race = function (promises) {
        // 返回一个promise对象
        return new Promise((resolve, reject) => {
            // 遍历promises数组获取promise结果
            promises.forEach((p, index) => {
                Promise.resolve(p).then(
                    // 第一个成功的promise，直接改变状态
                    // 为什么这是第一个成功的呢？因为这是异步回调，会把所有的回调函数都添加进回调队列，
                    // 当状态改变后就会执行回调函数
                    value => {
                        resolve(value)
                    },
                    reason => {
                        reject(reason)
                    }
                )
            })
        })
    }

    window.Promise = Promise;
})(window)
```

参考资料

- [尚硅谷Promise教程(promise前端进阶)](https://www.bilibili.com/video/BV1MJ41197Eu?p=19)