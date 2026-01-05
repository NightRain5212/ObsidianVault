- CSS (Cascading Style Sheets) 是用来描述HTML文档样式的语言，它定义了网页元素的外观和布局。

## 基础语法结构

- CSS 规则由两个主要部分组成：选择器和声明块。
```css
selector {
  property: value;
  property: value;
  /* 更多属性和值 */
}
```
- **选择器(selector)**：指定要样式化的HTML元素
- **声明块**：包含在大括号 `{}` 内的一组声明
- **声明**：由属性和值组成，用冒号 `:` 分隔，以分号 `;` 结束

## 选择器

- **元素选择器**：选择HTML元素
```css
p {
  color: blue;
}
```

- **类选择器**：选择具有特定class属性的元素
```css
.highlight {
  background-color: yellow;
}
```

- **ID选择器**：选择具有特定id属性的元素
```css
#header {
  font-size: 24px;
}
```

- **属性选择器**：选择具有特定属性的元素
```css
a[target="_blank"] {
  color: red;
}
```

- **后代选择器（Descendant Selector）：**通过指定元素的后代关系选择 HTML 元素。
- 后代选择器使用空格分隔元素名称。
- 如下代码，div p 选择器将选择所有在 \<div\> 元素内的 \<p\> 元素。
``` css
div p {
  font-weight: bold;
}
```

- 选择所有元素
```css
* {
	margin: 0px;
}
```



## 使用css的方式

- **内联样式**：直接在HTML元素中使用style属性
```html
<p style="color: red;">这是红色文字</p>
```

- **内部样式表**：在HTML文档的`<head>`中使用`<style>`标签
```html
<head>
  <style>
    body {
      background-color: lightblue;
    }
  </style>
</head>
```

- **外部样式表**：链接到外部.css文件
```html
<head>
  <link rel="stylesheet" href="styles.css">
</head>
```

## 盒子模型

- CSS盒子模型(Box Model)是CSS布局的核心概念，它描述了元素在页面中所占的空间以及如何计算这些空间。
## 盒子模型的组成部分

每个HTML元素都被视为一个矩形盒子，由内到外包括：

1. **内容区域(Content)** - 包含元素的实际内容（文本、图片等）
2. **内边距(Padding)** - 内容与边框之间的透明区域
3. **边框(Border)** - 围绕内容和内边距的边界线
4. **外边距(Margin)** - 盒子与其他元素之间的透明区域


### 标准盒子模型（content-box，默认）

- `width`和`height`只定义内容区域的尺寸
    
- 总宽度 = width + padding-left + padding-right + border-left + border-right
    
- 总高度 = height + padding-top + padding-bottom + border-top + border-bottom
    
### 替代盒子模型（border-box）

- `width`和`height`定义内容+内边距+边框的总尺寸
    
- 内容区域宽度 = width - padding-left - padding-right - border-left - border-right
    
- 内容区域高度 = height - padding-top - padding-bottom - border-top - border-bottom


## Flex弹性布局

- Flexbox（弹性盒子布局）是CSS3中一种现代的布局模式，它提供了更有效的方式来布局、对齐和分配容器内项目的空间，即使**它们的大小是未知或动态的**。
- Flex布局由**Flex容器（父元素** 和**Flex项目（子元素）** 组成：
```html
<div class="flex-container">  <!-- 容器 -->
  <div class="flex-item">1</div>  <!-- 项目 -->
  <div class="flex-item">2</div>
  <div class="flex-item">3</div>
</div>
```

```css
.flex-container {
  display: flex; /* 或 inline-flex */
}
```

### 容器属性

#### 1. flex-direction - 主轴方向

- `row`（默认）：水平从左到右
- `row-reverse`：水平从右到左
- `column`：垂直从上到下
- `column-reverse`：垂直从下到上

#### 2. flex-wrap - 换行方式

- `nowrap`（默认）：不换行
- `wrap`：换行，第一行在上方
- `wrap-reverse`：换行，第一行在下方

#### 3.justify-content - 主轴对齐

- `flex-start`（默认）：左对齐
- `flex-end`：右对齐
- `center`：居中
- `space-between`：两端对齐，项目间隔相等
- `space-around`：每个项目两侧间隔相等
- `space-evenly`：项目与项目、项目与边框间隔相等

#### 4.align-items - 交叉轴对齐

- `stretch`（默认）：拉伸填满容器高度
- `flex-start`：交叉轴起点对齐
- `flex-end`：交叉轴终点对齐
- `center`：交叉轴中点对齐
- `baseline`：项目第一行文字基线对齐

## `@import url()`

- `@import url()` 是 **CSS 中的一种规则**，用于在当前 CSS 文件中**导入其他外部样式表**。它的作用类似于 HTML 中的 `<link>` 标签，但它是纯 CSS 的引入方式。
- **必须位于所有其他 CSS 规则之前**

## `z-index`

- `z-index` 是 CSS 中控制元素堆叠顺序（垂直于屏幕的 Z 轴方向）的属性，用于决定元素在三维空间中的前后关系。
- 具有更高堆叠顺序的元素总是在较低的堆叠顺序元素的前面
- 注意
	1. **必须配合定位属性**：
	    - 只有 `position` 值为 `relative`/`absolute`/`fixed`/`sticky` 时生效
	    - `static` 定位的元素会忽略 `z-index`
	2. **父子关系限制**
	    - 子元素永远不能突破父堆叠上下文的限制
	    - 即使子元素 `z-index` 很大，也无法显示在父元素上下文外的上层
	3. **性能考虑**：
	    - 过多使用高 `z-index` 可能导致渲染性能问题
	    - 建议控制在合理范围（如 1-1000）

## `text-decoration`

- 设置**文本装饰效果**
	- 下划线（underline）
	- 删除线（line-through）
	- 上划线（overline）
	- 波浪线（wavy）等
- 去除超链接下划线
```css
a {
  text-decoration: none; /* 移除默认蓝色下划线 */
  color: #333;          /* 通常同时修改默认蓝色 */
}
```

## `position`

- 定位类型
	- `static`： 元素遵循正常文档流，**默认值**
	- `reltive`：相对定位
	- `absolute`：绝对定位
	- `fixed`：固定定位
	- `sticky`：粘性定位
- 非`static`定位可设置属性：
	- `top`, `right`, `bottom`, `left`：指定元素边缘与包含块对应边缘的距离
	- `z-index`：控制堆叠顺序

### `relative`

- **不脱离文档流**：元素仍然占据原来的空间
- **相对自身定位**：通过top/right/bottom/left相对于元素原始位置进行偏移
- **创建定位上下文**：为绝对定位的子元素提供参照基准,**作为绝对定位子元素的容器**
- **不影响其他元素布局的调整**

### `absolute`

- **脱离文档流**：元素不再占据空间
- **相对于最近的非static定位祖先**：如果没有则相对于初始包含块(通常是html)
- **不限制于父元素内**：可以定位到页面任何位置
- **可能覆盖其他元素**

### `fixed`


- **相对于视口(viewport)定位**：不受页面滚动影响
- **完全脱离文档流**：不占据原空间，不影响其他元素布局
- **需要指定定位坐标**：至少设置 `top`/`right`/`bottom`/`left` 中的一个
- 粘性定位的元素是依赖于用户的滚动，在 **position:relative** 与 **position:fixed** 定位之间切换。
- 它的行为就像 **position:relative;** 而当页面滚动超出目标区域时，它的表现就像 **position:fixed;**，它会固定在目标位置。
- 这个特定阈值指的是 top, right, bottom 或 left 之一
### `sticky`


- **混合定位模式**：
    - 在阈值范围内表现如 `relative`
    - 超过阈值后表现如 `fixed`
- **不脱离文档流**：始终占据原空间
- **必须指定阈值**：至少设置 `top`/`right`/`bottom`/`left` 中的一个


### **绝对定位容器**实现**水平垂直居中**


- **`left: 50%` + `top: 50%`**  
	- 将容器的**左上角**定位到父容器的正中心
- **`transform: translate(-50%, -50%)`**  
	- 将容器**向左上方移动自身宽高的50%**，使容器中心与父容器中心对齐


## 伪类

- CSS伪类是选择器的一种特殊类型，它允许你基于元素的**特定状态**而非文档结构来选择元素。伪类以单冒号(`:`)开头，用于定义元素的特殊状态或关系。
- 语法
```css
selector:pseudo-class {
	property:value;
}
```
### 用户交互状态伪类

|伪类|描述|
|---|---|
|`:hover`|鼠标悬停在元素上时的状态|
|`:active`|元素被激活(如鼠标点击)时的状态|
|`:focus`|元素获得焦点时的状态(如表单输入)|
|`:focus-within`|元素或其子元素获得焦点时的状态|
|`:focus-visible`|元素通过键盘获得焦点时的状态|
### 结构位置伪类

|伪类|描述|
|---|---|
|`:first-child`|选择父元素的第一个子元素|
|`:last-child`|选择父元素的最后一个子元素|
|`:nth-child(n)`|选择父元素的第n个子元素|
|`:nth-last-child(n)`|选择父元素的倒数第n个子元素|
|`:only-child`|选择没有兄弟元素的元素|
|`:first-of-type`|选择同类型兄弟元素中的第一个|
|`:last-of-type`|选择同类型兄弟元素中的最后一个|
### 表单相关伪类

|伪类|描述|
|---|---|
|`:checked`|被选中的单选按钮或复选框|
|`:disabled`|被禁用的表单元素|
|`:enabled`|可用的表单元素|
|`:valid`|输入内容符合验证规则的表单元素|
|`:invalid`|输入内容不符合验证规则的表单元素|
|`:required`|标记为必填的表单元素|
|`:optional`|非必填的表单元素|

## 伪元素

- 伪元素是CSS中的特殊选择器，它允许你**为元素的特定部分设置样式**或**在文档中插入虚拟内容**。
- 伪元素以双冒号(`::`)开头(在CSS3中推荐使用双冒号，但单冒号`:`也支持以保持向后兼容)。
- 语法
```css
selector::pseudo-element {
    property: value;
}
```

### `::before`&`::after`

- 这两个伪元素用于在选定元素的**内容前/后**插入生成的内容
- **特点**：
	- 必须设置`content`属性才会显示
	- 默认是行内元素(inline)
	- 插入的内容不可被选中


### `first-line`

- 选择元素文本的**第一行**，无论窗口大小如何变化。

### `first-letter`

- 选择元素文本的**第一个字母**或第一个字符。

### `selection`

- 定义用户**选中文本**时的样式。

### `placeholder`

- 设置表单元素**占位文本**的样式。

## `transform`

- 用于对元素进行**2D或3D变换**的属性，它允许你旋转、缩放、倾斜或平移元素而不影响文档流中的其他元素。
- 语法
```css
transform: none|transform-functions;
```

### `translate`位移变换

| 函数                   | 描述        | 示例                                      |
| -------------------- | --------- | --------------------------------------- |
| `translateX(x)`      | 水平移动元素    | `transform: translateX(50px)`           |
| `translateY(y)`      | 垂直移动元素    | `transform: translateY(-20%)`           |
| `translate(x,y)`     | 同时水平和垂直移动 | `transform: translate(10px, 20px)`      |
| `translate3d(x,y,z)` | 3D空间移动    | `transform: translate3d(0, 10px, -5px)` |
- 百分比值相对于元素自身尺寸
- 不影响文档流布局
- 性能优于直接修改`top`/`left`

### `rotate`旋转变换

| 函数                      | 描述                   | 示例                                 |
| ----------------------- | -------------------- | ---------------------------------- |
| `rotate(angle)`         | 2D旋转(单位deg/rad/turn) | `transform: rotate(45deg)`         |
| `rotateX(angle)`        | 沿X轴3D旋转              | `transform: rotateX(180deg)`       |
| `rotateY(angle)`        | 沿Y轴3D旋转              | `transform: rotateY(45deg)`        |
| `rotateZ(angle)`        | 沿Z轴3D旋转(同rotate)     | `transform: rotateZ(90deg)`        |
| `rotate3d(x,y,z,angle)` | 自定义旋转轴和角度            | `transform: rotate3d(1,1,0,45deg)` |
### `scale`缩放变换

|函数|描述|示例|
|---|---|---|
|`scale(x)`|同时缩放X和Y轴|`transform: scale(1.5)`|
|`scaleX(x)`|仅缩放X轴|`transform: scaleX(0.8)`|
|`scaleY(y)`|仅缩放Y轴|`transform: scaleY(1.2)`|
|`scale(x,y)`|分别缩放X和Y轴|`transform: scale(1.2, 0.8)`|
|`scale3d(x,y,z)`|3D空间缩放|`transform: scale3d(1,1,2)`|

## `transition`

- 用于**平滑过渡**元素从一个状态到另一个状态的属性，它可以让 CSS 属性值的变化在一定时间内渐变完成，而不是瞬间变化。
- 语法
```css
transition: [property] [duration] [timing-function] [delay];
```
### 组成部分

### 1. `transition-property`

- 指定应用过渡效果的 CSS 属性名称

	- 可以指定单个属性：`transition-property: width;`
	- 或多个属性：`transition-property: width, height;`
	- 或所有可过渡属性：`transition-property: all;` (默认值)

- **可过渡属性示例**：
	- 尺寸：`width`, `height`
	- 颜色：`color`, `background-color`
	- 位置：`top`, `left`
	- 变换：`transform`
	- 透明度：`opacity`
	- 边框：`border-radius`

### 2. `transition-duration`

- 指定过渡效果的持续时间
### 3. `transition-timing-function`

- 定义过渡效果的速度曲线

| 值             | 描述             | 等效贝塞尔曲线                         |
| ------------- | -------------- | ------------------------------- |
| `ease` (默认)   | 慢速开始，然后加速，最后减速 | `cubic-bezier(0.25,0.1,0.25,1)` |
| `linear`      | 匀速运动           | `cubic-bezier(0,0,1,1)`         |
| `ease-in`     | 慢速开始           | `cubic-bezier(0.42,0,1,1)`      |
| `ease-out`    | 慢速结束           | `cubic-bezier(0,0,0.58,1)`      |
| `ease-in-out` | 慢速开始和结束        | `cubic-bezier(0.42,0,0.58,1)`   |
### 4. `transition-delay`

- 定义过渡效果开始前的延迟时间
- 默认值：`0s` (无延迟)

## `filter`

- 对元素（通常是图片）应用**图形效果**的属性，它允许你直接在浏览器中实现类似 Photoshop 的滤镜效果，无需修改原始图像文件。
```css
filter: none | <filter-function> [<filter-function>]*;
```
### `blur()`

- 创建模糊效果
- 参数：模糊半径（单位 px/rem 等）
- 性能消耗较大，慎用于大面积元素

### `brightness()`

- 调整亮度
### `opacity()`

- 调整透明度
### `saturate()`

- 调整饱和度


## 背景div设置

```html
<div class="background"> </div>
```

```css
.background {
    /* 设置背景图片 */
    background: url(...) no-repeat;
    
    /* 背景图片定位在元素中心 */
    background-position: center;
    
    /* 背景图片缩放方式：覆盖整个元素区域 */
    background-size: cover;
    
    /* 元素高度为100%视口高度 */
    height: 100vh;
    
    /* 元素宽度为100%父容器宽度 */
    width: 100%;
}
```