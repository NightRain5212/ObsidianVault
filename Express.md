- Express.js 是一个基于 Node.js 平台的极简、灵活的 web 应用开发框架，它提供了一系列强大的特性来帮助创建各种 web 和移动设备应用。
- 最小服务器框架
```js
const express = require('express');
const app = express();
const port = 3000;

// 定义路由
app.get('/', (req, res) => {
  res.send('Hello World!');
});

// 启动服务器
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

## 路由处理

```js
// GET 请求
app.get('/users', (req, res) => {
  res.send('Get all users');
});

// POST 请求
app.post('/users', (req, res) => {
  res.send('Create a new user');
});

// PUT 请求
app.put('/users/:id', (req, res) => {
  res.send(`Update user with ID ${req.params.id}`);
});

// DELETE 请求
app.delete('/users/:id', (req, res) => {
  res.send(`Delete user with ID ${req.params.id}`);
});
```

## 中间件

- 在请求(Request)和响应(Response)周期中执行的函数，可以访问请求对象(`req`)、响应对象(`res`)和应用程序的请求-响应循环中的下一个中间件函数(`next`)。
- 中间件函数可以执行以下任务：
	1. 执行任何代码
	2. 修改请求和响应对象
	3. 结束请求-响应周期
	4. 调用堆栈中的下一个中间件
- 