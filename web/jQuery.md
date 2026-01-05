
- jQuery 是一个快速、简洁的 JavaScript 库，它简化了 HTML 文档遍历、事件处理、动画和 Ajax 交互等操作。

## 引入

```html
<!-- 使用 CDN 引入 -->
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
```

## 基础语法

```js
$(selector).action()
```

- `$` 是 jQuery 的别名
- `selector` 用于"查询" HTML 元素
- `action()` 是对元素执行的操作

## 选择器

```js
$("#id")            // ID 选择器
$(".class")         // 类选择器
$("element")        // 标签选择器
$("*")              // 全选选择器
$("selector1, selector2") // 多选选择器
```

