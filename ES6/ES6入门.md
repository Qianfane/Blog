## 1. let 和 const 命令

**let、const**

- 不存在变量提升
- 暂时性死区
    - 在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。
- 不允许重复声明
- 块级作用域

## 2. 变量的结构赋值
解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。

## 3. 箭头函数

箭头函数有几个使用注意点。

（1）箭头函数没有自己的`this`对象，内部的`this`就是定义时上层作用域中的`this`。 

（2）不可以当作构造函数，也就是说，不可以对箭头函数使用`new`命令，否则会抛出一个错误。

（3）不可以使用`arguments`对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

（4）不可以使用`yield`命令，因此箭头函数不能用作 Generator 函数。

适用场景：与`this`无关的场景

不适用场景：

- 对象的方法，且该方法内部包括`this`；

  ```js
  const cat = {
    lives: 9,
    jumps: () => {
      this.lives--;
    }
  }
  ```

  使用普通函数：`this`指向`cat`；使用箭头函数：`this`指向全局对象。

- 需要动态`this`的时候，也不应使用箭头函数

  ```js
  var button = document.getElementById('press');
  button.addEventListener('click', () => {
    this.classList.toggle('on');
  });
  ```

  上面代码运行时，点击按钮会报错，因为`button`的监听函数是一个箭头函数，导致里面的`this`就是全局对象。如果改成普通函数，`this`就会动态指向被点击的按钮对象。 

## 4. class

constractor  和 方法是 定义在原型上的，可以给实例用

static 静态方法是给类自己使用的，可以被子类继承



















