# Vue 源码分析

本文分析 `vue` 作为一个 `MVVM` 框架的基本实现原理。主要从==数据代理==、==模板解析==和==数据绑定==三方面分析。

## 一、准备

1. `[].slice.call(lis)`: 将伪数组转换为真数组

    > 伪数组本质上是对象，只不过是拥有类似数组的 `length` 属性和下标。比如：`const lis = document.getElementsByTagName('li')`，`lis` 就是伪数组
    >
    > 通过 `Array.prototype.slice` 和 `call` 方法可以把伪数组转换为真数组

2. `node.nodeType`: 得到节点类型
    节点类型一般有4种：`document`, `element`, `attribute`, `text`

3. `Object.defineProperty(obj, propertyName, {})`: 给对象添加属性(属性描述符)

    > 不支持 `IE8`，因为 `defineProperty` 方法不支持 `IE8`

    `obj`: 被添加属性的对象

    `propertyName`: 属性名

    `{}`: 属性描述符（面试）

    * 属性描述符分两类
        * 数据描述符
            `configurable`: 是否可以重新定义
            `enumerable`: 是否可以枚举`(for..in / keys())`
            `value`: 指定初始值
            `writable`: value是否可以修改
        * 访问描述符
            `get`: 回调函数，根据其他相关的属性值动态计算得到当前属性值
            `set`: 回调函数，监视当前属性值的变化，更新其他相关的属性值

4. `Object.keys(obj)`: 得到对象自身可枚举属性组成的数组

5. `obj.hasOwnProperty(prop)`: 判断 `prop` 是否是 `obj` 自身的属性

6. `DocumentFragment`: 文档碎片(高效批量更新多个节点)

    理解
    * `document`: 对应显示的界面，包含n个`element`，一旦更新`document`内部的某个元素界面更新
    * `documentFragment`: 内存中保存n个`element`的容器对象（不与界面关联），如果更新`fragment`中的某个`element`，界面不变
## 二、数据代理(MVVM.js)
1. 数据代理是什么？

   通过一个对象代理对另一个对象属性的操作（读/写）

   > 怎么去理解代理呢，其实就相当于中间人。比如说你必须去取一样东西，你又没空去，就找了一个中间人去帮你拿，那么这个中间人就是你的代理人。

2. `vue` 中数据代理是什么？

   通过==`vm` 对象==来代理==`data` 对象==中所有属性的==操作==

   > 读操作：用 `vm.xxx` 去获取数据
   >
   > 写操作：用 `vm.xxx = xxx` 去修改数据

3. 好处是什么？

   更方便的操作 `data` 中的数据

4. 基本实现流程

   1）通过 ==`Object.defineProperty()`== 给 ==`vm` 对象==添加与 `data` 对象的属性对应的==属性描述符==

   2）==`vm` 对象==中所有新添加的属性都包含 ==`setter/getter`== 

   3）在 ==`setter/getter`== 里面==操作== `data` 对象对应的的==属性数据==

```js
// 以下代码非源码（别开枪，自己人）
Object.defineProperty(me, key, {
  configurable: false, // 不能再重新定义
  enumerable: true, // 可以枚举
  // 当通过vm.name读取属性值时自动调用
  get: function proxyGetter() {
    // 读取data中对应属性值返回(实现代理读操作)
    return me._data[key];
  },
  // 当通过vm.name = 'xxx'时自动调用
  set: function proxySetter(newVal) {
    // 将最新的值保存到data中对应的属性上(实现代理写操作)
    me._data[key] = newVal;
  }
});
```



## 三、模板解析(compile.js)

模板可以理解为 `HTML` 里面嵌套了 `JS` 代码，分两种：大括号表达式和指令。

1. 模板解析基本流程
  1). 把 `el` 中的所有子节点取出来放进 `fragment` 容器中
  2). 对 `fragment` 中的所有层次子节点递归进行编译解析处理
  3). 把解析后的 `fragment` 添加到 `el` 中显示

```js
function Compile(el, vm) {
  // 保存vm
  this.$vm = vm;
  // 保存el元素
  this.$el = this.isElementNode(el) ? el : document.querySelector(el);
  // 如果el元素存在
  if (this.$el) {
    // 1. 取出el中所有子节点, 封装在一个framgment对象中
    this.$fragment = this.node2Fragment(this.$el);
    // 2. 编译fragment中所有层次子节点
    this.init();
    // 3. 将fragment添加到el中
    this.$el.appendChild(this.$fragment);
  }
}
```



2. 模板解析1：大括号表达式解析
   1). 根据正则对象得到匹配出的表达式字符串
   2). 从 `data` 中取出表达式对应的属性值
   3). 将文本节点中的 `textContent` 设置为属性值

   > 比如说解析 `<p>{{name}}</p>`，分三步。
   >
   > 第一步，取出 `name`；
   >
   > 第二步，从 `data` 中获取 `name` 的值；
   >
   > 第三步，将这个值设置为文本节点的 `textContent`。
   >
   > 搞定！

3. 模板解析2：事件指令解析
   1). 从指令名中得到事件名
   2). 根据指令值（表达式）从 `methods` 得到对应的事件处理函数对象
   3). 给当前元素节点绑定指定事件名和回调函数的DOM事件监听
   4). 移除解析后的事件指令属性

   > 比如说解析 `<button v-on:click="show">提示</button>`，分四步。
   >
   > 第一步，得到事件名 `click`；
   >
   > 第二步，从 `methods` 中得到事件回调函数 `show`；
   >
   > 第三步，给当前节点绑定点击事件监听，回调函数就是 `show`；
   >
   > 第四步，移除 `v-on:click="show"` 这个指令。

4. 模板解析3：一般指令解析
   1). 得到指令名和指令值（表达式）  `text/html/chass`  `msg/myClass`
   2). 根据指令值从 `data` 中得到相对应的表达式
   3). 根据指令名确定要操作元素节点的属性
   `v-text` -- `textContent` 属性
   `v-html` -- `innerHTML` 属性
   `v-class` -- `className` 属性
   4). 将得到的表达式的值设置到对应的属性上
   5). 移除解析后的指令属性

   > 比如说解析 `v-test=“msg”`，分五步。
   >
   > 第一步，得到指令名 `test` 和指令值 `msg`；
   >
   > 第二步，根据 `msg` 从 `data` 中取出 `msg` 的具体值；
   >
   > 第三步，根据 `test` 确定去操作元素节点的 `textContent`；
   >
   > 第四步，设置 `textContent` 为 `msg` 的具体值；
   >
   > 第五步，移除解析后的 `v-test`

## 四、数据劫持-->数据绑定-->双向数据绑定

### 4.1 数据绑定

数据绑定涉及到两个方面：==数据==和==界面==

理解：data中的数据一旦发生改变，界面也会跟着变化

### 4.2 数据劫持

1）理解：数据劫持是 `vue` 中实现数据绑定的一种技术（技巧）

2）基本思想：用 `defineProperty()` 给 `data` 中的属性添加了 `set` 函数，一旦数据发生变化就去更新界面。

### 4.3 流程图

分两部分：初始化和更新数据

1. 在初始化时，首先是 `new MVVM`，
   - 这个时候会去创建一个 `Observer`，它是什么作用呢？我们通过 `Observer` 就可以去劫持/监视 `data` 数据里所有层次的属性数据；
   - 同时我们去创建 `Dep`，这个 `Dep` 和属性数据是一一对应的；
   - 接下来我们去创建 `Compile`，它呢用于解析指令/大括号表达式，解析完了就可以去初始化显示视图了，通过 `Updater` 去显示视图；
   - 在创建 `Compile` 的时候我们会==为表达式创建对应的 `Watcher`，并指定相对应的更新函数==；
   - 然后 `Watcher` 会和 `Dep` 去建立联系，这个过程叫==添加订阅者==，在 `Dep` 里面有一个 `subs`,它是一个保存n个 `Watcher` 的数组容器。到这里我们的初始化流程就讲完了
2. 接下来说一下更新数据时候的流程
   - 当数据更新，比如 `vm.name = ‘Tom’`，这个时候 `data` 数据的变化会被 `Observer` 的 `setter` 监听到，就会去通知 `Dep` 数据发生变化啦， `Dep` 呢，会去通知所有相关的 `Watcher`，然后 `Watcher` 就会去通过 `Updater` 函数去更新视图。

![数据绑定原理（响应式原理）流程图.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640419010949-7e4f7f65-1e94-4351-8f0e-b0b225b8fffd.png?x-oss-process=image%2Fresize%2Cw_1041%2Climit_0) 

### 4.4 双向数据绑定

> 对双向数据绑定理解：数据更新，页面更新；页面更新，数据也就更新。

那么其实它是建立在单向数据绑定的基础之上的，也就是数据更新了，页面就要更新。

> 然后再加上对页面上DOM元素进行监听，当DOM元素改变了，就去修改相对应的属性数据的值。

双向数据绑定（View-->Model）的实现流程：

* 在解析 `v-model` 指令时，给当前元素添加 `input` 监听；
* 在 `input` 的 `value` 发生变化时，将最新值赋值给相对应的 `data` 属性。



