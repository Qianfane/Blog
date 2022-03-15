## CSS盒模型

[TOC]

### 1 理解

------

#### 1.1 理解（包括作用）

CSS中将每一个元素都设置为了一个矩形的盒子，将所有的元素都设置为盒子，是为了方便页面的布局，当这些元素都是盒子以后，我们的布局就变成了在页面中摆放盒子。

#### 1.2 构成

- 内容(`content`)、内填充(`padding`)、边框(`border`)、外边距(`margin`)

#### 1.3 分类

- 标准`(W3C)`盒模型：元素宽度（`width`） = `content`
- 怪异`(IE)`盒模型：元素宽度（`width`） = `content + padding + border`

#### 1.4 box-sizing 

- `box-sizing: content-box;` 默认的标准盒模型元素效果
- `box-sizing: border-box;` 触发怪异盒模型元素的效果
- `box-sizing: inherit;` 继承父元素 `box-sizing` 属性的值

### 2 延伸

------

#### 2.1 JS如何设置获取盒模型对应得宽和高（==未完待续==）

- `dom.style.width/height`:只能取内联样式的宽/高
- `dom.currentStyle.width/height`:取渲染后的宽/高，也就是说三种写法都能取，但只支持 `IE`
- `window.getComputedStyle(dom).width/height`:和上一条一样效果，且兼容所有浏览器
- `dom.getBoundingClientRect().width/height`:？

#### 2.2 示例题（根据盒模型解释边距重叠）（==未完待续==）

#### 2.3 BFC（边距重叠解决方案）（==未完待续==）