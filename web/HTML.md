- HTML (HyperText Markup Language) 是用于创建网页的标准标记语言
- 基础语法
```html
<!DOCTYPE html>
<html>
<head>
    <title>页面标题</title>
</head>
<body>
    <!-- 页面内容 -->
    <h1>这是一个标题</h1>
    <p>这是一个段落。</p>
</body>
</html>
```

- `<!DOCTYPE html>` 声明文档类型为HTML5
    
- `<html>` 是HTML文档的根元素
    
- `<head>` 包含文档的元信息(meta)，如标题、字符集、样式表链接等
    
- `<body>` 包含可见的页面内容
    
- 标签通常是成对出现的，如 `<p>` 和 `</p>`
    
- 有些标签是自闭合的，如 `<img>`, `<br>`, `<input>`

## 头部

- HTML 头部包含 HTML [`<head>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Elements/head) 元素的内容，页面在浏览器加载后它的内容不会在浏览器中显示，它的作用是保存页面的一些[元数据](https://developer.mozilla.org/zh-CN/docs/Glossary/Metadata)。



## 块级元素&内联元素

- 块级元素在页面中以块的形式展现。一个块级元素出现在它前面的内容之后的新行上。任何跟在块级元素后面的内容也会出现在新的行上。块级元素通常是页面上的结构元素。例如，一个块级元素可能代表标题、段落、列表、导航菜单或页脚。一个块级元素不会嵌套在一个内联元素里面，但它可能嵌套在另一个块级元素里面。
- 内联元素通常出现在块级元素中并环绕文档内容的一小部分，而不是一整个段落或者一组内容。内联元素不会导致文本换行。它通常与文本一起使用，例如，[`<a>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Elements/a) 元素创建一个超链接，[`<em>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Elements/em) 和 [`<strong>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Reference/Elements/strong) 等元素创建强调。
- 不是所有元素都拥有开始标签、内容和结束标签。一些元素只有一个标签，通常用来在此元素所在位置插入/嵌入一些东西。这些元素被称为[空元素](https://developer.mozilla.org/zh-CN/docs/Glossary/Void_element)。

## 基础标签

### 文档结构标签

- `<html>`: 定义HTML文档
    
- `<head>`: 包含文档的元信息
    
- `<body>`: 包含可见的页面内容
    
- `<title>`: 定义浏览器工具栏中的标题
    

### 文本标签

- `<h1>` 到 `<h6>`: 定义标题，h1最大，h6最小
    
- `<p>`: 定义段落
    
- `<br>`: 插入换行
    
- `<hr>`: 创建水平线
    
- `<!-- 注释内容 -->`: HTML注释
    

### 格式化标签

- `<b>` 或 `<strong>`: 加粗文本
    
- `<i>` 或 `<em>`: 斜体文本
    
- `<u>`: 下划线文本
    
- `<sup>`: 上标文本
    
- `<sub>`: 下标文本
    

### 链接和图片

- `<a href="url">链接文本</a>`: 创建超链接
    
- `<img src="image.jpg" alt="描述">`: 插入图像
    

### 列表

- `<ul>`: 无序列表
    
- `<ol>`: 有序列表
    
- `<li>`: 列表项
    

### 表格

- `<table>`: 定义表格
    
- `<tr>`: 定义表格行
    
- `<th>`: 定义表头单元格
    
- `<td>`: 定义表格数据单元格
    

### 表单

- `<form>`: 定义表单
    
- `<input>`: 定义输入字段
    
- `<textarea>`: 定义多行文本输入
    
- `<button>`: 定义可点击按钮
    
- `<select>`: 定义下拉列表
    
- `<option>`: 定义下拉列表中的选项
    

## `<div>` 与 `<span>` 

|特性|`<div>`|`<span>`|
|---|---|---|
|显示类型|块级元素（block）|行内元素（inline）|
|默认行为|独占一行|与其他行内元素同在一行|
|主要用途|布局和组织大块内容|包裹小段文本或行内元素|
|包含内容|可以包含其他块级元素|通常只包含文本或其他行内元素|

## `<textarea>` 标签

- **作用**：创建多行文本输入控件
- **特点**：
    
    - 允许用户输入多行文本
    - 可以通过`rows`和`cols`属性指定可见的行数和列数
    - 可以使用CSS设置大小

## `<i>`标签引入icon

```html
<!-- 通过CDN引入 -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

<!-- 使用示例 -->
<i class="fas fa-home"></i> 首页
<i class="fas fa-user"></i> 用户
<i class="fas fa-search"></i> 搜索
```

## video标签

```html
<video src="movie.mp4" controls>
  您的浏览器不支持 video 标签。
</video>
```

## `<label>`

- `<label>` 标签是 HTML 中用于**关联表单控件**的重要元素
- for绑定
```html
<label for="目标元素的id"> 标签文本 </label>
<input type="..." id="目标元素的id">
```
- 嵌套绑定
```html
<label>
  记住密码：
  <input type="checkbox" name="remember">
</label>
```

## 特殊字符

|原义字符|等价字符引用|
|---|---|
|<|`&lt;`|
|>|`&gt;`|
|"|`&quot;`|
|'|`&apos;`|
|&|`&amp;`|


## 基础属性

- 大多数HTML元素可以拥有属性，提供关于元素的额外信息。属性总是以名称/值对的形式出现，如：`name="value"`。
- 属性包含元素的额外信息，这些信息不会出现在实际的内容中。
- 有时你会看到没有值的属性，这也是完全可以接受的。这些属性被称为**布尔属性**。布尔属性只能有一个值，这个值一般与属性名称相同。

### 语法结构

属性必须包含：
- 一个空格，它在属性和元素名称之间。如果一个元素具有多个属性，则每个属性之间必须由空格分隔。
- 属性名称，后面跟着一个等于号。
- 一个属性值，由一对引号（""）引起来。

### 通用属性

- `id`: 定义元素的唯一ID
    
- `class`: 定义元素的类名(用于CSS和JavaScript)
    
- `style`: 定义元素的内联CSS样式
    
- `title`: 定义元素的额外信息(作为工具提示显示)
    

### 链接属性

- `href`: 指定链接的目标URL
    
- `target`: 指定在何处打开链接文档(_blank, _self, _parent, _top)
    

### 图片属性

- `src`: 指定图像的URL
    
- `alt`: 指定图像的替代文本
    
- `width`: 指定图像的宽度
    
- `height`: 指定图像的高度
    

### 表单属性

- `action`: 指定表单提交的URL
    
- `method`: 指定表单提交的HTTP方法(GET或POST)
    
- `name`: 指定表单的名称
    
- `type`: 指定输入元素的类型(text, password, submit, radio, checkbox等)
    
- `value`: 指定输入元素的值
    
- `placeholder`: 指定输入字段的提示文本
    

### 其他常用属性

- `disabled`: 禁用元素
    
- `readonly`: 指定输入字段为只读
    
- `required`: 指定输入字段为必填项
    
- `maxlength`: 指定输入字段的最大字符数

## `<header>`

- `<header>` 是HTML5引入的**语义化标签**，用于表示页面或某个内容区块的页眉/顶部区域，通常包含标题、导航、Logo等内容。

## `<nav>`

- `<nav>` 是 **HTML5 引入的语义化标签**，专用于定义页面的**导航链接区域**。它帮助浏览器、屏幕阅读器和搜索引擎识别页面中的主要导航结构。
```html
<header>
    <nav>
        <a href="/">网站Logo</a>
        <a href="/products">产品</a>
        <a href="/blog">博客</a>
    </nav>
</header>
```
