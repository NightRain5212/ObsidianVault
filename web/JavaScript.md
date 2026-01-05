
## 基本语法

- 变量声明
```js
// ES5
var x = 10; // 函数作用域

// ES6+
let y = 20; // 块级作用域，可重新赋值
const z = 30; // 块级作用域，不可重新赋值
```

- 数据类型
```js
// 基本类型
// 字符串
let str = "Hello";
let str2 = 'World';
let template = `模板字符串 ${str}`;

// 数字
let num = 123;
let float = 3.14;
let infinity = Infinity;

// 布尔
let bool = true;

// null
let empty = null;

// undefined
let notDefined;

// Symbol (ES6)
let sym = Symbol('unique');


// 引用类型

// 对象
let obj = { 
  key: 'value',
  method: function() {}
};

// 数组
let arr = [1, 'two', false];

// 函数
function myFunc() {}
```

- 增强for循环
```js
// for...of (ES6)
for (let item of array) {
  console.log(item);
}

// for...in (遍历对象属性)
for (let key in object) {
  console.log(key, object[key]);
}
```

- 错误处理
```js
try {
  // 可能出错的代码
  nonExistentFunction();
} catch (error) {
  // 错误处理
  console.error('出错:', error.message);
} finally {
  // 无论是否出错都会执行
  console.log('执行完成');
}
```

- 解构赋值
```js
// 数组解构
let [a, b] = [1, 2];  // a=1, b=2

// 对象解构
let {name, age} = {name: 'Bob', age: 30};
```

- 展开运算符
```js
let arr1 = [1, 2];
let arr2 = [...arr1, 3, 4]; // [1, 2, 3, 4]

let obj1 = {a: 1};
let obj2 = {...obj1, b: 2}; // {a: 1, b: 2}
```

- 导出模块
```js
// math.js
export const PI = 3.14159;
export function square(x) {
  return x * x;
}
```

- 导入模块
```js
// app.js
import { PI, square } from './math.js';
console.log(square(PI));
```

## 函数

- 函数声明时，函数名前加上`function`关键字即可
```js
function print(s) {
	console.log(s);
};

function add(x,y) {
	return x+y;
};

//函数表达式，将函数赋值给变量
const sum = function(a,b) {
	return a+b;
};

// 默认参数 (ES6)
function greet(name = 'Guest') {
  console.log(`Hello, ${name}`);
}

// 剩余参数 (ES6)
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b);
}
```
### 箭头函数

- 不绑定自己的 `this`、`arguments`、`super` 或 `new.target`。
```js
const sum = (a,b) => {
	return a+b;
}；

//简化形式
const sum = (a,b) => a+b;
const square = x => x*x;
```

- 简化
	- **单参数**：可省略括号
	- **单行表达式**：可省略大括号和 `return` (隐式返回)
### 函数提升

- 指的是在代码执行前，JavaScript 引擎会将函数声明和变量声明"提升"到它们所在作用域的顶部。
- **函数声明会完全被提升**，包括函数名和函数体
- **函数表达式不会被提升**，只有变量声明会被提升，函数赋值部分不会
- **箭头函数的实现永远不会被提升**

### 回调函数

- 回调函数(Callback Function)是 JavaScript 中一种重要的编程模式，它是作为参数传递给另一个函数，并在特定条件满足或操作完成后被调用的函数。
- 实例
```js
function greet(name, callback) {
  console.log(`Hello, ${name}!`);
  callback(); // 执行回调函数
}

function sayGoodbye() {
  console.log('Goodbye!');
}

greet('Alice', sayGoodbye); 

/* 输出：
Hello, Alice!
Goodbye!
*/
```

## 数组

- 创建数组
```js
// 字面量方式
const fruits = ['苹果', '香蕉', '橙子'];

// 构造函数方式
const numbers = new Array(1, 2, 3);

// 创建空数组
const emptyArray = [];
const anotherEmpty = new Array();

// 创建指定长度的空数组
const length5Array = new Array(5); // [empty × 5]
```

- 常见方法

|方法|描述|是否修改原数组|返回值|示例|
|---|---|---|---|---|
|`push()`|末尾添加一个或多个元素|✅|新长度|`arr.push('a')`|
|`pop()`|删除并返回最后一个元素|✅|删除的元素|`arr.pop()`|
|`unshift()`|开头添加一个或多个元素|✅|新长度|`arr.unshift('a')`|
|`shift()`|删除并返回第一个元素|✅|删除的元素|`arr.shift()`|
|`splice()`|添加/删除任意位置元素|✅|被删除的元素数组|`arr.splice(1,0,'x')`|
|`concat()`|合并多个数组|❌|新数组|`arr1.concat(arr2)`|
|`slice()`|截取子数组|❌|新数组|`arr.slice(1,3)`|
|`fill()`|填充数组元素|✅|修改后的数组|`arr.fill(0)`|
- 遍历与转换方法

| 方法              | 描述        | 是否修改原数组 | 返回值       | 示例                               |
| --------------- | --------- | ------- | --------- | -------------------------------- |
| `forEach()`     | 对每个元素执行函数 | ❌       | undefined | `arr.forEach(x=>console.log(x))` |
| `map()`         | 映射为新数组    | ❌       | 新数组       | `arr.map(x=>x*2)`                |
| `filter()`      | 过滤符合条件的元素 | ❌       | 新数组       | `arr.filter(x=>x>5)`             |
| `reduce()`      | 累计为单个值    | ❌       | 累计结果      | `arr.reduce((a,b)=>a+b,0)`       |
| `reduceRight()` | 从右向左累计    | ❌       | 累计结果      | `arr.reduceRight((a,b)=>a+b)`    |
| `flat()`        | 扁平化嵌套数组   | ❌       | 新数组       | `arr.flat(2)`                    |
| `flatMap()`     | 先映射后扁平化   | ❌       | 新数组       | `arr.flatMap(x=>[x,x*2])`        |
| `join()`        | 数组转字符串    | ❌       | 字符串       | `arr.join(',')`                  |
| `toString()`    | 数组转字符串    | ❌       | 字符串       | `arr.toString()`                 |

-  搜索与排序方法

| 方法              | 描述           | 是否修改原数组 | 返回值          | 示例                      |
| --------------- | ------------ | ------- | ------------ | ----------------------- |
| `indexOf()`     | 查找元素首次出现位置   | ❌       | 索引/-1        | `arr.indexOf('a')`      |
| `lastIndexOf()` | 查找元素最后出现位置   | ❌       | 索引/-1        | `arr.lastIndexOf('a')`  |
| `includes()`    | 检查是否包含某元素    | ❌       | boolean      | `arr.includes('a')`     |
| `find()`        | 查找第一个符合条件的元素 | ❌       | 元素/undefined | `arr.find(x=>x>5)`      |
| `findIndex()`   | 查找第一个符合条件的索引 | ❌       | 索引/-1        | `arr.findIndex(x=>x>5)` |
| `sort()`        | 排序数组         | ✅       | 排序后的数组       | `arr.sort((a,b)=>a-b)`  |
| `reverse()`     | 反转数组顺序       | ✅       | 反转后的数组       | `arr.reverse()`         |

## 对象

- 对象是键值对的集合.
- **引用类型**：对象是引用类型，赋值和传参传递的是引用
- **动态属性**：可以随时添加或删除属性
- **原型继承**：每个对象都有一个原型链
- **属性描述符**：每个属性都有配置特性（可写、可枚举、可配置）
```js
var user = {
	name: "aaa";  //属性
	age: 18
	getName: function () {
		return "aaa";
	} //方法
}
//调用对象的属性
console.log(user.name);
console.log(user.getname());
// 删除age属性
delete user.age; 
```

## Math对象

- `Math.abs()`绝对值
- `Math.min()`最小值，`Math,max()`最大值
- `Math.round()` 四舍五入，`Math.ceil()`向上取整，`Math.floor()`向下取整
- `Math.random()`返回0~1的伪随机数
```js
//返回min~max的随机数
function getrandom(min,max) {
	var res = Math.random() * (max-min) + min
}
```
- 幂和开方
```js
Math.pow(2, 3)    // 8 - 2的3次方(ES6中也可用 2 ** 3)
Math.sqrt(16)     // 4 - 平方根
Math.cbrt(8)      // 2 - 立方根(ES6)
Math.hypot(3, 4)  // 5 - 所有参数平方和的平方根(勾股定理)
```

## Date对象

- Date 对象是 JavaScript 中用于**处理日期和时间**的内置对象，它可以表示从 1970 年 1 月 1 日 UTC（Unix 纪元）以来的毫秒数。

### 创建新对象

```js
const now = new Date(); // 创建当前时间的Date对象

// 从Unix时间戳（毫秒）创建
const dateFromTimestamp = new Date(1654051200000); 

// 从Unix时间戳（秒）创建
const dateFromSeconds = new Date(1654051200 * 1000);

// 从字符串创建
const dateFromString = new Date("2023-06-01");
const dateTimeFromString = new Date("June 1, 2023 12:00:00");

// 指定年月日创建
// 注意：月份从0开始（0=1月，11=12月）
const specificDate = new Date(2023, 5, 1, 12, 30, 0, 0); 
// 2023年6月1日12:30:00.000
```

### 获取时间戳

- 时间戳：从**固定参考时间点**（通常是1970年1月1日00:00:00 UTC）开始计算的**经过的秒数或毫秒数**。

```js
const timestamp = Date.now(); // 当前时间戳（毫秒）
const timestamp2 = new Date().getTime(); // 同上
const timestamp3 = +new Date(); // 使用+运算符转换
```
### 获取具体时间

```js
const date = new Date();


date.getFullYear();  // 年份（4位数）
date.getMonth();     // 月份（0-11）
date.getDate();      // 日期（1-31）
date.getDay();       // 星期（0-6，0=星期日）
date.getHours();     // 小时（0-23）
date.getMinutes();   // 分钟（0-59）
date.getSeconds();   // 秒（0-59）
date.getMilliseconds(); // 毫秒（0-999）
date.getTime();      // 时间戳（ms）
```

### 设置时间

```js
const date = new Date();

date.setFullYear(2024);
date.setMonth(11);      // 设置月份（11=12月）
date.setDate(25);       // 设置日期
date.setHours(15);      // 设置小时
date.setMinutes(30);    // 设置分钟
date.setSeconds(0);     // 设置秒
date.setMilliseconds(0); // 设置毫秒

// 同样有UTC版本的设置方法
date.setUTCFullYear(2024);
```

### 日期格式化

```js
const date = new Date();

date.toString();       // "Wed Jun 01 2023 12:30:00 GMT+0800 (中国标准时间)"
date.toDateString();   // "Wed Jun 01 2023"
date.toTimeString();   // "12:30:00 GMT+0800 (中国标准时间)"
date.toLocaleString(); // "2023/6/1 12:30:00"（根据系统区域设置）
date.toLocaleDateString(); // "2023/6/1"
date.toLocaleTimeString(); // "12:30:00"
date.toISOString();    // "2023-06-01T04:30:00.000Z"（ISO格式）
date.toJSON();         // "2023-06-01T04:30:00.000Z"（同toISOString）
```
- 自定义格式化
```js
function formatDate(date) {
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const day = String(date.getDate()).padStart(2, '0');
  return `${year}-${month}-${day}`;
}

formatDate(new Date()); // "2023-06-01"
```

## DOM

- DOM (Document Object Model) 是 JavaScript 操作网页内容的接口，它将 HTML 和 XML 文档表示为树形结构，使程序能够动态访问和更新文档的内容、结构和样式。
- 含义
	- **文档对象模型**：将 HTML/XML 文档表示为节点树
	- **编程接口**：提供操作文档的方法和属性
	- **跨平台标准**：由 W3C 标准化，各浏览器实现

### 节点

- 在 DOM (Document Object Model) 中，**节点(Node)** 是构成文档树的基本单位，代表了文档结构中的每一个组成部分。DOM 将整个文档抽象为一个由各种类型节点组成的树形结构。

#### 节点类型

-  文档节点 (Document Node)
-  文档类型节点 (DocumentType Node)
-  属性节点 (Attribute Node)
-  元素节点 (Element Node)
-  文本节点 (Text Node)
-  注释节点 (Comment Node)
-  nodeType属性，不同节点对应不同nodeType。
- `document.nodeType   - 9`
### 节点树

```
document (根节点)
├─ <html>
   ├─ <head>
   │   ├─ <title>
   │   └─ <meta>
   └─ <body>
       ├─ <h1>
       ├─ <div>
       │   ├─ <p>
       │   └─ <p>
       └─ <footer>
```

- 节点关系
```
parentNode        // 父节点
childNodes        // 子节点列表
firstChild        // 第一个子节点
lastChild         // 最后一个子节点
nextSibling       // 下一个兄弟节点
previousSibling   // 上一个兄弟节点
```

## Document方法

- 获取元素
```js
// 通过ID获取
document.getElementById('header')

// 通过类名获取(返回HTMLCollection)
document.getElementsByClassName('item')

// 通过标签名获取(返回HTMLCollection)
document.getElementsByTagName('div')

// 通过name属性获取(返回NodeList)
document.getElementsByName('username')

// CSS选择器查询单个元素(返回NodeList)
document.querySelector('#main .item')

// CSS选择器查询所有匹配元素(返回NodeList)
document.querySelectorAll('div.highlight')
```

### 节点操作

- 创建节点
```js

// 创建元素节点
document.createElement('div')

// 创建文本节点
document.createTextNode('文本内容')

// 创建文档片段(优化性能)
document.createDocumentFragment()

// 创建注释节点
document.createComment('注释内容')

```
- 添加节点

```js
// 末尾添加子节点
parentElement.appendChild(newNode)

// 在指定节点前插入
parentElement.insertBefore(newNode, referenceNode)

// 现代插入方法
referenceNode.before(newNode)   // 在前面插入
referenceNode.after(newNode)    // 在后面插入
parentElement.prepend(newNode)  // 作为第一个子节点
parentElement.append(newNode)   // 作为最后一个子节点
```

- 删除/替换

```js
// 删除子节点
parentElement.removeChild(childNode)

// 现代删除方法
element.remove()

// 替换节点
parentElement.replaceChild(newNode, oldNode)

// 现代替换方法
oldNode.replaceWith(newNode)
```

### 属性操作

```js
// 获取/设置属性
element.id = 'newId'
const value = element.getAttribute('data-custom')
element.setAttribute('data-custom', 'value')

// 移除属性
element.removeAttribute('disabled')

// 检查属性
element.hasAttribute('title')
```

### 类名操作

```js
// classList API
element.classList.add('active')
element.classList.remove('active')
element.classList.toggle('active')
element.classList.contains('active')

// className属性
element.className = 'class1 class2'
```

## 事件处理

### HTML事件

- HTML 事件是**发生在 HTML 元素上的"事情"**，可以是浏览器行为（如页面加载完成）或用户行为（如点击按钮）。
- 可以直接在 HTML 元素中指定事件处理程序
```html
<button onclick="demo()">点击我</button>
<script>
	function demo() {
		alert("hello")
	}
</script>
```

### DOM0级事件

- DOM0级事件是早期的事件处理方式，也被称为"传统事件模型"。
- 同一事件只能绑定一个处理函数。
```js
//先获取元素，在设置事件
const btn = document.getElementById('myButton');
btn.onclick = function() {
  alert('DOM0级事件');
};
```

### DOM2级事件

- DOM2级事件是更现代的事件处理模型，提供了更强大的功能。
- 可以为同一事件添加多个处理函数
```js
const btn = document.getElementById('myButton');

// 添加事件监听
btn.addEventListener('click', function() {
  console.log('第一个处理函数');
});

// 添加第二个处理函数（不会覆盖前一个）
btn.addEventListener('click', function() {
  console.log('第二个处理函数');
});

// 移除事件监听
const handler = function() {
  console.log('将被移除的处理函数');
};
btn.addEventListener('click', handler);
btn.removeEventListener('click', handler);
```

### 事件冒泡

- 事件冒泡是指当一个DOM元素上的事件被触发时，该事件会从**目标元素**开始，**向上逐级传播**到DOM树的最顶层节点（通常是document对象）的过程。

#### 事件传播的阶段

1. **捕获阶段（Capture Phase）**：
    - 从window对象向下传播到目标元素的父级
    - 使用`addEventListener`的第三个参数`true`可以监听此阶段
    - 从最外层祖先元素向内传播
2. **目标阶段（Target Phase）**：
    - 事件到达目标元素本身
    - 无论是否设置捕获都会触发
    - 如果事件不可冒泡，传播到此结束
3. **冒泡阶段（Bubble Phase）**：
    - 从目标元素向上传播回window对象
    - 大多数事件默认在此阶段触发处理程序

### 事件代理

- 事件代理是一种利用事件冒泡机制的高效事件处理模式，它通过将事件监听器设置在父元素而非子元素上来管理多个子元素的事件。
- **事件冒泡机制**：当子元素触发事件时，事件会向上冒泡到父元素
- **目标元素识别**：通过`event.target`可以识别实际触发事件的子元素
- **统一处理**：在父元素上设置一个监听器处理所有子元素的事件

### 事件对象

- 事件对象是事件发生时浏览器自动创建的，包含了与该事件相关的所有信息。**当事件触发时**，浏览器会将这个事件对象**作为参数传递给事件处理函数**。

- 基本属性
```js
event.type         // 事件类型（如"click"、"mouseover"等）
event.target       // 触发事件的元素（事件起源的元素）
event.currentTarget // 当前正在处理事件的元素（绑定事件的元素）
event.bubbles      // 事件是否冒泡
event.cancelable   // 事件是否可以取消默认行为
event.timeStamp    // 事件发生的时间戳
event.eventPhase   // 事件阶段（1=捕获，2=目标，3=冒泡）

//鼠标事件
event.clientX / event.clientY // 鼠标相对于浏览器窗口的坐标
event.pageX / event.pageY     // 鼠标相对于文档的坐标（包含滚动）
event.screenX / event.screenY // 鼠标相对于屏幕的坐标
event.button                 // 被按下的鼠标按钮（0=左键，1=中键，2=右键）
event.buttons                // 按下的所有鼠标按钮（位掩码）
event.relatedTarget          // 相关元素（如mouseover时的来源元素）

//键盘事件
event.key         // 按下的键的字符串表示（如"a"、"Enter"）
event.code        // 物理键码（如"KeyA"、"Enter"）
event.keyCode     // 已废弃，但仍有浏览器支持
event.which       // 已废弃，但仍有浏览器支持
event.altKey      // Alt键是否按下
event.ctrlKey     // Ctrl键是否按下
event.shiftKey    // Shift键是否按下
event.metaKey     // Meta键（Mac的Cmd键）是否按下
event.repeat      // 按键是否被按住重复触发
```

- 常见方法
```js
event.preventDefault() // 阻止元素的默认行为
event.stopPropagation() // 停止事件在DOM中的传播（冒泡或捕获）
event.stopImmediatePropagation() // 停止传播并阻止同元素的其他处理函数执行
```
### 常见事件

#### 鼠标事件

```js
// 单击事件
element.addEventListener('click', function(event) {
  console.log('元素被点击', event.clientX, event.clientY);
});

// 双击事件
element.addEventListener('dblclick', function() {
  console.log('元素被双击');
});

// 右键菜单事件
element.addEventListener('contextmenu', function(event) {
  event.preventDefault(); // 阻止默认右键菜单
  console.log('右键点击');
});

// 进入/离开元素边界时触发一次
// 鼠标移入
element.addEventListener('mouseenter', function() {
  console.log('鼠标进入元素');
});

// 鼠标移出
element.addEventListener('mouseleave', function() {
  console.log('鼠标离开元素');
});

// 进入/离开子元素也会触发
// 鼠标移入
element.addEventListener('mouseover', function() {
  console.log('鼠标进入元素');
});

// 鼠标移出
element.addEventListener('mouseout', function() {
  console.log('鼠标离开元素');
});


// 鼠标移动（会频繁触发）
element.addEventListener('mousemove', function(event) {
  console.log(`鼠标位置: X=${event.clientX}, Y=${event.clientY}`);
});

// 鼠标按下/释放
element.addEventListener('mousedown', function() {
  console.log('鼠标按钮按下');
});

element.addEventListener('mouseup', function() {
  console.log('鼠标按钮释放');
});
```

#### 键盘事件

```js
// 键按下
document.addEventListener('keydown', function(event) {
  console.log('按下键:', event.key, '键码:', event.keyCode);
});

// 键释放
document.addEventListener('keyup', function(event) {
  console.log('释放键:', event.key);
});

// 产生字符的按键
document.addEventListener('keypress', function(event) {
  console.log('字符键:', event.key);
});

document.addEventListener('keydown', function(event) {
  // 检测Enter键
  if (event.key === 'Enter') {
    console.log('按下了Enter键');
  }
  
  // 检测组合键 Ctrl+S
  if (event.ctrlKey && event.key === 's') {
    event.preventDefault(); // 阻止保存网页
    console.log('按下了Ctrl+S');
  }
});

```

#### 表单事件

```js

// 值改变时触发（失去焦点后）
inputElement.addEventListener('change', function() {
  console.log('输入值改变:', this.value);
});

// 实时输入事件
inputElement.addEventListener('input', function() {
  console.log('正在输入:', this.value);
});

// 获取焦点
inputElement.addEventListener('focus', function() {
  console.log('输入框获得焦点');
});

// 失去焦点
inputElement.addEventListener('blur', function() {
  console.log('输入框失去焦点');
});

// 值改变并失去焦点后触发
input.addEventListener('change', function() {
  console.log('最终值:', this.value);
});

// 文本被选中时触发
input.addEventListener('select', function(event) {
  const selectedText = this.value.substring(
    this.selectionStart, 
    this.selectionEnd
  );
  console.log('选中文本:', selectedText);
});


// 重置表单
form.addEventListener('reset', function() {
  console.log('表单将被重置');
  // 可以在这里添加确认对话框
});

// 提交事件
formElement.addEventListener('submit', function(event) {
  // 阻止默认表单提交
  event.preventDefault();
  
  // 表单验证
  if (!validateForm()) {
    return;
  }
  
  // 手动提交
  console.log('表单提交数据:', getFormData());
});
```

#### 窗口事件

```js
// 整个页面（包括资源）加载完成
window.addEventListener('load', function() {
  console.log('页面完全加载');
});

// DOM内容加载完成（不等待图片等资源）
document.addEventListener('DOMContentLoaded', function() {
  console.log('DOM加载完成');
});

// 窗口大小改变（需要节流）
window.addEventListener('resize', function() {
  console.log(`窗口大小: ${window.innerWidth}x${window.innerHeight}`);
});

// 页面滚动（需要节流）
window.addEventListener('scroll', function() {
  console.log('滚动位置:', window.scrollY);
});
```

## 定时器

- 用于延迟执行代码或定期重复执行代码。
- 注意`setTimeout`中的`this`指向全局环境。

- `setTimeout`:   延迟执行（执行一次）
```js
// 格式(接受两个参数，推迟执行的函数，推迟执行的时间，返回定时器编号)
var timerid = setTimeout(func, delay)

console.log('开始');

setTimeout(() => {
  console.log('3秒后执行');
}, 3000);

console.log('定时器已设置');
/* 输出顺序：
开始
定时器已设置
3秒后执行
*/
const timeoutId = setTimeout(() => {
  console.log('这段代码不会执行');
}, 5000);

// 在3秒后取消定时器
setTimeout(() => {
  clearTimeout(timeoutId);  
  console.log('定时器已取消');
}, 3000);
```
- `setInterval`：间隔执行（无限执行）
```js
let count = 0;
const intervalId = setInterval(() => {
  count++;
  console.log(`第${count}次执行`);
  if (count >= 5) {
    clearInterval(intervalId); // 停止定时器
  }
}, 1000);
```

## 闭包

- 全局变量的作用域是全局性的，即在整个JavaScript程序中，全局变量处处都在。
- 而在函数内部声明的变量，只在函数内部起作用。这些变量是局部变量，作用域是局部性的；函数的参数也是局部性的，只在函数内部起作用。
- 闭包是一种**保护私有变量**的机制，它在函数执行时创建一个私有作用域，从而**保护内部的私有变量不受外界干扰**。
- 直观地说，闭包就像是一个不会被销毁的栈环境。

- 例子:`inner`函数就是一个闭包，它可以访问`outer`函数作用域中的`count`变量，即使`outer`函数已经执行完毕。
```js
function outer() {
    let count = 0;
    
    function inner() {
        count++;
        console.log(count);
    }
    
    return inner;
}

const closureFn = outer();
closureFn(); // 输出 1
closureFn(); // 输出 2
closureFn(); // 输出 3
```
- **内存消耗**：闭包会使函数中的变量一直保存在内存中，可能导致内存泄漏。不再使用的闭包应及时解除引用。
    
- **性能考虑**：过度使用闭包可能会影响脚本性能，因为闭包的作用域链比普通函数更长。



## 防抖

- 防抖是一种控制函数执行频率的技术，它可以确保一个函数在一定时间间隔内只执行一次，特别适合处理高频触发的事件。
- 防抖含义
	- 当事件被频繁触发时
	- 只有在事件停止触发后的指定时间间隔内不再有新触发
	- 才会执行一次事件处理函数
```js
function debounce(f,delay) {
	let timer = null;
	return function () {
		if(timer) {
			clearTimeout(timer);
		}
		timer = setTimeout(f,delay);
	};
}
```
###  工作原理

1. **首次调用**：
    - `timer`为`null`，跳过`clearTimeout`
    - 设置一个新的定时器，计划在`delay`毫秒后执行`f`
2. **在`delay`时间内再次调用**：
    - `timer`不为`null`（有定时器存在）
    - 清除之前的定时器（取消之前计划的执行）
    - 设置一个新的定时器，重新开始计时
3. **停止调用`delay`时间后**：
    - 没有新的调用来清除定时器
    - 定时器到期，执行原始函数`f`

## 节流

- 节流(Throttle)是一种控制函数执行频率的技术，它可以确保函数在一定时间间隔内最多执行一次，特别适合处理高频触发的事件。
- 节流含义
	- 当事件被频繁触发时
	- 无论触发多频繁，函数都会按照固定的时间间隔执行
	- 确保执行频率不会超过设定的限制

```js
function throttle(f,delay) {
	var valid = true;
	return function() {
		if(!valid) {
			return false;
		}
		valid = false;
		setTimeout(function() {
			f();
			valid = true;
		},delay);
	};
}
```

### 工作原理

1. **首次调用**：
    - `valid`为`true`，允许执行
    - 设置`valid`为`false`阻止后续调用
    - 设置定时器，在`delay`毫秒后执行函数并重置`valid`
2. **在`delay`时间内再次调用**：
    - `valid`为`false`，直接返回不执行
    - 保持节流状态
3. **定时器到期后**：
    - 执行原始函数`f`
    - 重置`valid`为`true`
    - 允许下一次调用

