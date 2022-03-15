## 一、 AJAX 编程基础
### 01 AJAX 基础

#### 1.1 什么是AJAX及其作用

AJAX，全称为Asynchronous JavaScript And XML， 意为异步的JS 和 XML，通过AJAX可以在浏览器中向服务器发送异步请求

#### 1.2 AJAX的特点

**优点**

* 无刷新获取数据
* 允许根据用户事件更新部分页面内容

**缺点**

* 没有浏览历史，不可回退
* 存在跨域问题（同源）
* SEO 不友好

#### 1.3 服务器返回数据的形式

曾用过XML，后来被JSON取代
* XML 简介
    * 可扩展标示语言（Extensive Markup Language），被设计用来传输和存储数据，和 HTML 类似，不同的是 HTML 中都是预定义标签，而 XML 中用的是自定义标签，用来存储数据。

### 02 AJAX 运行原理及实现

#### 2.1 运行原理

AJAX 相当于浏览器发送请求和接收响应的代理人，在不影响用户浏览页面的情况下，局部刷新页面数据，提高用户体验。

#### 2.2 AJAX实现步骤

客户端

```js
// 1. 新建AJAX对象
var xhr = new XMLHttpRequest()
// 2. 告诉AJAX对象请求方式和请求地址
xhr.open('get', 'http://localhost:3000/first')
// 3. 发送请求
xhr.send()
// 4. 获取服务器端响应到客户端的数据
xhr.onload = function () {
	console.log(xhr.responseText)
}
```

服务器端

```
安装express命令：npm i express
安装formidable命令：npm install --save formidable
启动命令：node app.js
```

#### 2.3 请求

1. ##### 请求参数传递
  
    GET 请求方式
    
    ```js
    // get 请求方式需要拼接请求参数
    // 配置 AJAX 对象时需要将请求参数拼在加"?"的请求地址后面
xhr.open('get', 'http://www.example.com?name=zhangsan&age=20');
    ```
    
    POST 请求方式
    
    ```js
    var params = 'username=' + nameValue + '&age=' + ageValue
    xhr.open('post', 'http://localhost:3000/post')
    // 设置请求参数格式的类型（post 请求一定要设置）
    xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
// 请求参数放这里
    xhr.send(params)
    ```
    
2. ##### 请求报文
  
    在 HTTP 请求和响应的过程中传递的数据块就叫报文，包括要传送的数据和一些附加信息，这些数据和信息要遵守规定好的格式
    
    * 报文头 向服务器端说的话
	* 报文体 存储数据
  
3. ##### 请求参数格式
  
    1. application/x-www-form-urlencoded
        ```js
        name=zhangsan&age=20&sex=男
        ```
    
    2. application/json
        ```js
        xhr.setRequestHeader('Contet-Type', 'application/json')
        xhr.send(JSON.stringify({name: 'zhangsan', age: 50}));
    	```

#### 2.4 两种获取服务器端响应方式

1. `onload` 事件
	
  ```js
  xhr.onload = function (){
      console.log(xhr.responseText)
	}
	```
2. `onreadystatechange` 事件
	
  ```js
	xhr.onreadystatechange = function () {
	    // AJAX 状态码: 2 3 4
	    console.log(xhr.readyState)
	    if (xhr.readyState == 4) {
	        console.log(xhr.responseText);
	    }
	}
	```

  
   **AJAX 状态码**
  
   * 获取: xhr.readyState
   * 值: 0 1 2 3 4
       * 0: 创建了 AJAX 对象，还没对 AJAX 对象进行配置(var xhr = new XMLHttpRequest)
       * 1: 配置了 AJAX 对象，还没发送请求(xhr.open('get', 'http://localhost:3000/readystate'))
       * 2: 已发送请求(xhr.send())
       * 3: 请求发送了，服务器端返回部分数据
       * 4: 请求发送了，服务器端返回全部数据


3. 两个事件比较
    onload 事件                  onreadystatechange 事件
    不兼容低版本IE                兼容低版本IE  
    不需判断 AJAX 状态码          需判断 AJAX 状态码
    被调用1次                     被调用多次
    
    推荐用 onload 性能高
    
  > 补充: 数据格式
  >
  > - 服务器响应回来的是 JSON 字符串，在客户端需要使用 JSON.parse() 把 JSON 字符串转换为对象，插入到 HTML 代码中，从而显示到网页上

4. `AJAX` 错误处理

* `400`: 可发请求，服务器端返回结果不是预期结果。用 `xhr.status` 获取 `http` 状态码根据具体情况处理
* `404`: 检查请求地址
* 500: 服务器端出错
* 断网: 触发 xhr 对象的 onerror 事件  在 onerror 事件处理函数中对错误进行处理

5. 低版本 IE 浏览器的缓存问题

```js
xhr.open('get', 'http://www.example.com?t=' + Math.random());
```
### 03 AJAX 封装

```js
var ajax = function (options) {
    var defaults = {
        type: 'get',
        url: '',
        data: {},
        header: {
            'Content-Type': 'application/x-www-form-urlencoded'
        },
        success: function () {},
        error: function () {}
    };

    Object.assign(defaults, options)

    var xhr = new XMLHttpRequest()
    var params = ''
    // 拼接请求参数
    for (var attr in defaults.data) {
        params += attr + '=' + defaults.data[attr] + '&'
    }
    // 截取掉最后一个字符 &
    params = params.substr(0, params.length - 1)
    // get 请求将请求参数拼接进请求地址
    if (defaults.type == 'get') {
        defaults.url = defaults.url + '?' + params
    }
    // 配置 AJAX 请求
    xhr.open(defaults.type, defaults.url)
    // 判断是否为 post 请求，是则执行以下代码
    if (defaults.type == 'post') {
        // 用户向服务器端发送的请求参数格式
        var contentType = defaults.header['Content-Type']
        // 设置 post 请求报文头
        xhr.setRequestHeader('Content-Type', contentType)
        // post 请求配置
        if (contentType == 'application/json') {
            // 向服务器端传递json数据格式的参数
            xhr.send(JSON.stringify(defaults.data))
        }else {
            // 向服务器端传递普通类型的请求参数
            xhr.send(params);
        }
    } else {
        // get 请求，直接发送请求
        xhr.send()
    }


    xhr.onload = function () {
        // 获取响应头中的数据
        var contentType = xhr.getResponseHeader('Content-Type')
        var responseText = xhr.responseText

        // 判断是否 json 对象
        if (contentType.includes('application/json')) {
            responseText = JSON.parse(responseText)
        }

        if (xhr.status == 200) {
            // 请求成功处理函数
            defaults.success(responseText, xhr)
        } else {
            // 请求失败处理函数
            defaults.error(responseText, xhr)

        }

    }
}

ajax({
    url: 'http://localhost:3000/responseData',
    data: {
        username: 'zs',
        age: 20
    },
    success: function (data, xhr) {
        console.log('这里是success函数')
        console.log(data)
    }
})
```
## 二、Ajax 编程扩展

### 01 AJAX 客户端模板引擎使用

```html
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
        <!-- 1. 将模板引擎的库文件引入到当前页面 -->
        <script src="/js/template-web.js"></script>
    </head>
    <body>
        <div id="container"></div>
        <!-- 2.准备art-template模板 -->
        <script type="text/html" id="tpl">
            <h1>{{username}} {{age}}</h1>
        </script>
        <script type="text/javascript">
            // 3.告诉模板引擎将那个数据和哪个模板进行拼接
            // 1) 模板id 2)数据 对象类型
            // 方法的返回值就是拼接好的html字符串
            var html = template('tpl', {username: 'zhangsan', age: 30});
            document.getElementById('container').innerHTML = html;
        </script>
    </body>
```

### 02 FormData

#### 2.1 FormData 对象的作用

> 请求方式必须为 post，因为 formData 对象要放在 send() 方法中

* 模拟HTML表单，相当于将HTML表单映射成表单对象，自动将表单对象中的数据拼接成请求参数的格式
* 异步上传二进制文件

#### 2.2 FormData 模拟表单

##### FormData 对象的使用

##### FormData 对象的实例方法

> get('key')                        获取表单对象属性的值
>
> set('key','value')            设置表单对象属性的值
>
> delete('key')                   删除表单对象的属性
>
> append('key','value')    追加表单对象属性

#### 2.3 FormData 二进制上传

> **上传步骤**
>
> 1. 用户选择文件时使用 onchange 事件监听
> 2. 用户所选择的文件保存在文件控件元素的 files 集合中，用 this.files[0] 获取
> 3. 把表单对象作为 xhr send() 方法的参数上传

具体代码如下：

```html
 <input type="file" id="file"/>
```

```js
var file = document.getElementById('file')
// 当用户选择文件的时候
file.onchange = function () {
    // 创建空表单对象
    var formData = new FormData();
    // 将用户选择的二进制文件追加到表单对象中
    formData.append('attrName', this.files[0]);
    // 配置ajax对象，请求方式必须为post
    xhr.open('post', 'www.example.com');
    xhr.send(formData);
 }
```



















