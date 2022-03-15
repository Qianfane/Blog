# `Vue-Router`

## 0. 相关理解

1) `Vue-Router`：官方提供的用来实现 `SPA` 的 `vue` 插件

2) 路由器：`Router`，主要用来管理路由。

3) 路由：`Route`，一种映射关系，一种键值对。

- `key`：`path`（路径）
- `value`（两种）
  - 前台路由：`component`（具体）
  - 后台路由：处理请求的回调函数（了解即可）

```js
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]
```

4) `SPA` 应用

1. 单页 `Web` 应用（`single page web application, SPA`）
2. 整个应用只有==一个完整的页面==
3. 点击页面中的导航连接==不会刷新==页面，只会做页面的==局部刷新==
4. 数据需要通过 `ajax` 请求获取

## 1. 基本使用

### 编写使用路由器

1. 创建路由器: `src/router/index.js`

   ```js
   // 创建 router 实例
   const router = new VueRouter({
   	// 配置路由
       ...
   })
   ```

2. 注册路由器：`Main.js`

   ```js
   import Vue from 'vue'
   import router from './router'
   // 创建 vue 配置路由器
   new Vue({
       el: '#app',
           router,
           render: h => h(app)
       }
   )
   ```

### 编写使用路由

1. 定义路由组件：`src/router/index.js`

2. 配置路由

   ```js
   // 创建 router 实例
   const router = new VueRouter({
       // 配置路由
       routes: [
           { path: '/foo', component: Foo },
           { path: '/bar', component: Bar }
       ]
   })
   ```

3. 使用路由：`App.vue`

   ```vue
   <p>
       <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
       <router-link to="/foo">Go to Foo</router-link>
       <router-link to="/bar">Go to Bar</router-link>
   </p>
   <!-- 路由出口 -->
   <!-- 路由匹配到的组件将渲染在这里 -->
   <router-view></router-view>
   ```

## 2. 嵌套路由

- 使用 `children` 配置子路由

  ```js
  const router = new VueRouter({
    routes: [
      {
        path: '/user/:id',
        component: User,
        children: [
          {
            path: 'profile',
            component: UserProfile
          },
          {
            path: 'posts',
            component: UserPosts
          }
        ]
      }
    ]
  })
  ```

## 3. 命名路由

​	当嵌套路由有很多层时，在 `<router-link>` 上写的 `path` 会很长，这时候我们可以用 `name` 来替代 `path`

- 在创建 `Router` 实例时，可以这么写

  ```js
  const router = new VueRouter({
    routes: [
      {
        path: '/user',
        name: 'user',
        component: User
      }
    ]
  })
  ```

- `<router-link>` 这么写

  ```vue
  <router-link :to="{ name: 'user' }}">User</router-link>
  ```

## 4. 路由组件传参

### 4.1 路由的 `query` 参数

 ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640076825543-84ed8aa7-36e7-44b4-99a7-22c9c9a197a1.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)  

### 4.2 路由的 `params` 参数

1. 配置路由，声明接收 `params` 参数

    ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640076633847-45db38d1-a5eb-42a8-be6d-06d35e6eced2.png) 

2. 传递参数

    ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640076705626-5d75675d-8580-4340-8e48-1c07b5165af7.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0) 

3. 接收参数

   ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640076734391-491b75c0-960d-4061-825f-44b87b887688.png) 

### 4.3 路由的 `props` 配置

 ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640077093760-b8a6f70a-8ff5-4638-b432-d11b5c7d2c8c.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0) 

接收参数

![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640077319250-984dc3c2-ae39-438d-82b0-7e3108f78676.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0) 

## 5. 编程式路由导航

1. 作用：不借助 `<router-link>` 实现路由跳转，让路由跳转更加灵活
2. 相关 `API`
   1. `this.$router.push()`：相当于点击路由链接(可以返回到当前路由界面)
   2. `this.$router.replace()`：相当于点击路由链接(不可以返回到当前路由界面)
   3. `this.$router.back()`：后退
   4. `this.$router.forward()`：前进
   5. `this.$router.go()`：可前进也可后退

## 6. 缓存路由组件对象

1. 理解：

   ​	默认情况下，我们离开一个路由组件时，这个路由组件会被卸载，再次回来就是新挂载的组件了，之前的数据都被清掉了；如果可以缓存路由组件对象，可以提高用户体验。

2. 具体编码

   ```vue
   <keep-alive include="News">
       <router-view></router-view>
   </keep-alive>
   ```

## 7. 两个新的生命周期钩子

1. 理解：路由组件所独有的两个钩子，用于捕获路由组件的激活状态
2. `API`
   - `activated`：路由组件被激活（展示）时触发
   - `deactivated`：路由组件失活（不展示）时被触发

## 8. 路由守卫

1. 作用：对路由进行权限控制

2. 分类：全局守卫、独享守卫、组件内守卫

3. 具体编码

   1. 全局守卫

       在路由配置文件里编码

       全局前置守卫：（初始化）`before` 是指在什么之前呢，路由切换或者说路由跳转

       全局后置守卫：同上 

      ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640183713355-f7b87c53-e368-4a22-bd5c-01556dff6150.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0) 

      

   2. 独享守卫

      在路由配置文件里编码

      只配置一个路由的守卫，别的路由都可以随意跳转，而且只有前置没有后置

      ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640183769242-4b5a21e4-f301-45f5-928b-b4022b19880b.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0) 

      

   3. 组件内守卫

      在组件内编码

      根据 `beforeRouteEnter` 和 `beforeRouteLeave`，可知，在进入组件和离开组件时调用

      ![image.png](https://cdn.nlark.com/yuque/0/2021/png/23138217/1640183822156-dc72a47a-d973-4213-9c60-0110bb15d9e8.png) 

## 9. 路由器的两种工作模式

**简单理解**

> 对 hash 值的理解
>
> 1. 对于一个 `url` 来说，什么是 `hash` 值？ -- `#` 及其后面的内容就是 `hash` 值
> 2. `hash` 值不会包含在 `HTTP` 请求中，即：`hash` 值不会带给服务器

1. `hash` 模式
   1. `#`：地址中永远带着 `#` ，不美观；
   2. 兼容性：兼容性较好；
   3. `#` 校验：若以后将地址通过第三方手机 `app` 分享，若 `app` 校验严格，则地址会被标记为不合法。
2. `history` 模式
   1. `#`：地址中不带 `#` ，干净美观；
   2. 兼容性：兼容性较差；
   3. 没有 `#` 出现问题：应用部署上线时需要后端人员支持，解决刷新页面服务端 `404` 的问题。

**原理**

1. `hash` 模式

   改变 `hash` 值方式：

   `hash` 模式的原理是 `window.onhashchange` 事件，监听 `hash` 值的变化，一旦变化之后，就去渲染相对应的路由组件。

1. `history` 模式

   `history.pushState`：改变 `url` 而不触发页面刷新，因为浏览器认为它是前进和后退的一种方式；

   `window.onpopstat`：监听浏览器的前进和后退。

   H5 新出了一个 `api`，让我们可以使用 `history.pushState` 去改变 `url` 而不触发页面刷新，因为浏览器认为它是前进和后退的一种方式；然后呢我们用 `window.onpopstat` 去监听浏览器的前进和后退，从而渲染相对应的路由组件。由于 `history` 使用的是规范的路由，和 `hash` 不一样，没有#，所以它会向服务器发起请求，这个时候呢就需要服务器的配合，无论路由如何改变，都不要给浏览器返回数据即可。

    ![未命名绘图.drawio (1).png](https://cdn.nlark.com/yuque/0/2022/png/23138217/1645171933968-f9800332-d15a-4e39-8735-1c4938bff136.png) 

## 10. 动态路由

一个路由组件 对应 多个路由，会根据路由传参的不同而作不同处理。

路由传参的params模式















