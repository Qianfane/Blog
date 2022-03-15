### 字符串转化为驼峰

```js
const str = 'get-element-by-id'

const fn = function(str) {
    return str.replace(/-\w/g, function(s) {
        return s.slice(1).toUpperCase()
    })
}
```

