# 第一章：Vue核心
## 1.1 vue的基本认识
* 是什么
    * 渐进式JavaScript框架
* 特点
    * 遵循MVVM模式
    * 编码简洁，体积小，运行效率高，适合移动/PC端开发
    * 本身只关注UI，可以轻松引入vue插件或其他第三库开发项目
* 怎么用
    * 插件
    * vue开发者工具（浏览器）
## 1.2 vue的基本使用
MVVM模式的理解
* M - model(data) 说白了就是数据 别纠结Model这个词
* V - view(页面)   
* VM - viewmodel(视图模型)
    * 双向数据绑定(Data Bindings)     内存和页面的数据一致
    * DOM事件监听(DOM Listeners)     页面发生变化了，就要作出相应改变，包括内存数据改变

## 1.3 模板语法(动态html页面的语法)
* 大括号表达式
    * {{msg}}
    * 功能：向页面输出数据
* 指令（以 v- 开头的自定义标签属性）
    * 例子
       * 强制数据绑定
            * v-bind: url="imgUrl"
            * 简写为：[v-bind]: url="imgUrl"
       * 绑定事件监听
            * v-on: click="xxx"
            * 简写为： @click="xxx"
## 1.4 计算属性和监视
* 计算属性
    * vm 对象中的 computed 属性对象中定义计算的方法
    * 页面中用{{方法名}}来显示计算的结果
* 监视
    * vm 对象中的 $watch{} 方法或 watch 配置来监视指定的属性
    * 当属性发生变化时，回调函数自动调用
* 计算属性高级
    * get：1. 什么时候回调？ 当读取属性值时回调，  2. 用来干嘛？ 根据相关数据计算并返回当前属性值
        * 计算属性存在缓存，多次读取只执行一次 getter 计算（面试时可能问到）
    * set（newVal, oldVal）：1. 什么时候回调？ 当前属性值变化时回调， 2. 用来干嘛？ 监视当前属性值变化，更新相关属性值
        * set 意思为监视，而非设置
* ps：回调函数号外
    * 什么是回调函数
        1. 自己定义的， 2. 我没有调用， 3. 最终执行了
    * 回调函数的两个点: 
        1. 什么时候调用， 2. 用来干嘛
    

## 1.5 class 与 style绑定
* 理解
  
    * 在 Vue 中通过 class 与 style 绑定来实现页面样式变化效果
* class 绑定
    * xxx是字符串：:class='xxx'
    * xxx是对象：:class='{classA: true, classB: false}'
    
    * xxx是数组（不常用）：:class='['classA', 'classB']'
* style 绑定  
    *   :style='{color: activeColor, fontSize: fontSize + 'px'}'
        * 其中 activeColor 和 fontSize 是 data 属性
    
    
    

## 1.6 条件渲染
* v-if 与 v-else：在显示时，把代码在内存移除
* v-show：隐藏一部分，代码存在
* 区别
    * 当频繁切换时，v-show 较好
      

## 1.7 列表渲染
* 遍历数组和对象
    ```html
    <ul>
        <li v-for="(p, index) in persons" :key="index"> <!-- v-for 的用法以及 :key 的用法-->
          {{index}}---{{p.name}}---{{p.age}}
          ---<button @click="deleteP(index)">删除</button>
          ---<button @click="updateP(index, {name: 'Cat', age: 20})">更新</button>
        </li>
    </ul>
    ```
    
* 列表过滤和排序    
    * 过滤：
        ```javascript
        fPersons = persons.filter(p => p.name.indexOf(searchName) !== -1)
        ```
        
    * 排序：
        ```javascript
        fPersons.sort(function (p1, p2) {
            if(orderType == 2) { // 2代表降序，如果参数是 a b , 返回的是 b-a
              return p2.age - p1.age
            } else {
              return p1.age - p2.age
            }
         })
        ```
        
## 1.8 事件处理
* 绑定监听
    * 两种方式
        * v-on:click="test1"
        * @click="test1"
    * 函数参数
        * 无event
        * 有event
            * 无 msg
            * 有 msg
                ```html
                    <div @click="test1"('atguigu', $event)'>这是一个div</div> // 注意是$event
                    test1(msg, event) {
                        ****
                    }
                ```
* 事件修饰符
    * 停止事件冒泡
        * @click.stop="test1"
    * 阻止事件默认行为
        * @click.prevent="test1"
* 按键修饰符
    * @click.13="test1"
    * @click.enter="test1"
      

## 1.9 表单输入绑定
* v-modle 与 value的完美配合，实现自动收集表单数据

## 1.10 实例生命周期
* 三个阶段
    * 初始化阶段
        * beforeCreate
        * create
        * beforeMount
        * mounted
    * 更新阶段
        * beforeUpdate
        * updated
    * 死亡阶段
        * beforeDestroy
        * destroyed
* 常用两个生命周期方法
    * mounted(): 初始化显示后立即调用，用来发送 ajax 请求
    * beforeDestroyed(): 做收尾工作，比如 清除定时器
    

## 1.11 效果（过渡 & 动画）
* 过渡（操作 css 的 trasition ）
    * transition 标签
        * 包裹在目标结构外面
    * style 样式
        * .xxx-enter-active：显示时的过渡效果
        * .xxx-leave-active：隐藏时的过渡效果
        
        * .xxx-enter：显示时的效果
        * .xxx-leave-to：隐藏时的效果
* 动画（不常用）（操作 css 的 animation ）    
  

## 1.12 过滤器
* 理解：
    * 功能：对要显示的数据进行特定格式化后再显示
    * 不改变原本数据，产生新的数据
* 使用
    * 定义过滤器
        * Vue.filter(filterName, function(value) {
            // 对数据进行一定处理
            return newValue
        })
    * 使用过滤器
        * <div>{{maData | filterName}}</div>
        * <div>{{maData | filterName(arg)}}</div>
    
    

## 1.13 内置指令与自定义指令

* 内置指令(面试时要能说出至少几个)
    * v-text
    * v-html
    * v-for
    * v-if
    * v-else
    * v-show
    * v-on
    * v-model
    
    * ref: 为某个元素注册一个唯一标识，vue对象通过$refs来访问这个元素对象
    * v-clock：防止闪现表达式，与 css 配合使用： [v-clock]: {display: none}
* 自定义指令
    * 全局指令
    * 局部指令
    * 使用指令
        v-my-directive='xxx'
    
    
    
    
    
    
    
    
    