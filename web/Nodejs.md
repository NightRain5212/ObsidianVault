- Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时环境，它让 JavaScript 能够脱离浏览器在服务器端运行。
- npm是包管理器
- 在终端运行`node xxx.js`，即可运行js文件

## 模块系统


```js
// 导入模块
const fs = require('fs');

// 导出模块（导出的是一个对象）
module.exports = {
  myFunction: () => {...},
  func2: () => {...}
};

// ES模块(新版Node支持)
import fs from 'fs';
export default myFunction;
```

## 全局对象

- 不需要引入第三方库即可使用的对象。
- Node.js 中默认没有 `window` 和 `document` 对象（这些是浏览器环境下的全局对象）

## 回调函数

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

## 类

- 定义
```js
class Person {
  // 构造函数
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  // 方法
  greet() {
    console.log(`Hello, my name is ${this.name} and I'm ${this.age} years old.`);
  }
}

// 使用类
const person1 = new Person('Alice', 30);
person1.greet(); // Hello, my name is Alice and I'm 30 years old.
```

- 类继承
```js
class Employee extends Person {
  constructor(name, age, jobTitle) {
    super(name, age); // 调用父类构造函数
    this.jobTitle = jobTitle;
  }
  
  work() {
    console.log(`${this.name} is working as a ${this.jobTitle}.`);
  }
}

const employee1 = new Employee('Bob', 25, 'Developer');
employee1.greet(); // 继承自Person的方法
employee1.work();  // Bob is working as a Developer.
```

- 静态属性（属于类本身，而不属于单个实例）
```js
class MathUtils {
  // 静态方法
  static square(x) {
    return x * x;
  }
  
  // 静态属性 (ES2022+)
  static PI = 3.14159;
}

console.log(MathUtils.square(5)); // 25
console.log(MathUtils.PI);       // 3.14159
```
## 事件

- Node.js 使用**事件循环**来实现非阻塞 I/O 操作，许多内置对象（如 HTTP 服务器、流等）都会触发事件。
- `EventEmitter` 是 Node.js 事件系统的核心类，位于 `events` 模块中。
```js
const events = require("events")
```

- 基本用法
```js
const EventEmitter = require('events');

// 创建事件发射器实例
const myEmitter = new EventEmitter();

// 注册事件监听器
myEmitter.on('event', () => {
  console.log('事件被触发!');
});

// 触发事件
myEmitter.emit('event');
```

- 自定义事件类
```js
const EventEmitter = require('events');

class MyClass extends EventEmitter {
  constructor() {
    super(); //调用父类构造函数
    this.value = 0;
  }
  
  increment() {
    this.value++;
    this.emit('incremented', this.value);
  }
}

const myInstance = new MyClass();
myInstance.on('incremented', (value) => {
  console.log('值增加到:', value);
});

myInstance.increment(); // 值增加到: 1
myInstance.increment(); // 值增加到: 2
```

## 同步&异步

- 在 Node.js 中，同步(Synchronous)和异步(Asynchronous)是两种不同的编程模式
### 同步操作 (Synchronous)

- **阻塞式**：代码按顺序执行，必须等待当前操作完成后才能执行下一行代码
    
- **线性执行**：像传统脚本一样从上到下逐行执行
    
- **简单直观**：更符合人类的线性思维习惯

### 异步操作 (Asynchronous)

- **非阻塞式**：发起操作后立即继续执行后续代码，操作完成后通过回调函数、Promise 或 async/await 处理结果
    
- **事件驱动**：基于事件循环机制
    
- **高效**：特别适合 I/O 密集型操作

## 文件读写

- Node.js 提供了多种方式进行文件系统操作，主要通过 `fs` (File System) 模块实现。以下是 Node.js 中进行文件读写的主要方法。
```js
const fs = require("fs"); //导入fs模块
```
- 异步读取
```js
// 文件名，编码，回调函数
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```
- 异步写入
```js
const content = '这是要写入的内容';
fs.writeFile('example.txt', content, 'utf8', (err) => {
  if (err) {
    console.error('写入文件出错:', err);
    return;
  }
  console.log('文件写入成功');
});
```

## 流&管道

- 流就像水流一样，数据像水一样"流动"，可以**一小块一小块地处理**，而不是一次性全部加载到内存中。
- 创建可读流
```js
const fs = require('fs');

// 创建可读流
const readStream = fs.createReadStream('largefile.txt', 'utf8');

readStream.on('data', (chunk) => {
  console.log(`接收到 ${chunk.length} 字节的数据`);
  // 可以逐块处理数据，而不是等待整个文件
});

readStream.on('end', () => {
  console.log('没有更多数据了');
});

readStream.on('error', (err) => {
  console.error('出错:', err);
});
```
- 创建可写流
```js
const fs = require('fs');

const writeStream = fs.createWriteStream('output.txt');

writeStream.write('第一行数据\n');
writeStream.write('第二行数据\n');
writeStream.end('最后一行数据'); // 结束流

writeStream.on('finish', () => {
  console.log('所有数据已写入');
});

writeStream.on('error', (err) => {
  console.error('写入出错:', err);
});
```
- 管道使用实例
```js
const fs = require('fs');

// 文件复制的最简单方式
fs.createReadStream('input.txt')
  .pipe(fs.createWriteStream('output.txt'))
  .on('finish', () => {
    console.log('文件复制完成');
  });
```

## http服务器

- 导入模块
```js
const http = require('http');
```
- 创建服务器实例
```js
var server = http.createServer((req,res)=> {
	//设置响应头
	res.writeHead(200,{
		'Content-Type': 'text/plain'
	})
	
    // 发送响应内容
    res.end('Hello, World!\n');
})

const PORT = 3000;
server.listen(PORT, ()=> {
	console.log(`服务器运行在 http://localhost:${PORT}`);
})
```
- 常见MIME类型：
	- `text/html` - HTML文档
	- `text/plain` - 纯文本
	- `application/json` - JSON数据
	- `application/xml` - XML数据
	- `image/png` - PNG图片

## 路由

- 路由是指根据URL路径和HTTP方法(GET, POST等)将请求映射到特定处理逻辑的机制。
- 根据不同地址返回不同数据
### 路由的三要素

- **路径(Path)**: 如 `/users`, `/posts/123`
- **HTTP方法**: GET, POST, PUT, DELETE等
- **处理函数**: 接收到匹配请求时执行的函数

### js实现

```js
const http = require('http');

const server = http.createServer((req, res) => {
  const { url, method } = req;
  
  // 简单路由实现
  if (url === '/' && method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end('<h1>首页</h1>');
  } 
  else if (url === '/users' && method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify([{ id: 1, name: '张三' }]));
  }
  else if (url === '/users' && method === 'POST') {
    // 处理POST请求...
  }
  else {
    res.writeHead(404, { 'Content-Type': 'text/html' });
    res.end('<h1>页面不存在</h1>');
  }
});

server.listen(3000);
```

## 使用get/post传输数据

### GET

####  基本特性

- **数据位置**：通过 URL 传递数据（查询字符串）
- **可见性**：数据在地址栏可见
- **缓存**：可以被浏览器缓存
- **历史记录**：保留在浏览器历史中
- **长度限制**：URL 有长度限制（通常约2048字符）
- **幂等性**：多次执行相同GET请求效果相同（安全方法）

```js
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  if (req.method === 'GET') {
	//获取get请求的参数
    const query = url.parse(req.url, true).query;
    console.log('GET参数:', query);
    res.end(`收到GET请求，参数: ${JSON.stringify(query)}`);
  }
});

server.listen(3000);
```

### POST

#### 基本特性

- **数据位置**：通过请求体(body)传递数据
- **可见性**：数据不在地址栏显示
- **缓存**：不会被浏览器缓存
- **历史记录**：不保留在浏览器历史中
- **长度限制**：理论上无限制（实际受服务器配置限制）
- **非幂等性**：多次提交可能产生不同结果

```js
const http = require('http');

const server = http.createServer((req, res) => {
  if (req.method === 'POST') {
    let body = '';
    req.on('data', chunk => {
      body += chunk.toString();
    });
    req.on('end', () => {
      console.log('POST数据:', body);
      res.end('收到POST请求');
    });
  }
});

server.listen(3000);
```