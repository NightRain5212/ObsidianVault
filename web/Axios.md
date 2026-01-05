- Axios 是一个基于 Promise 的 HTTP 客户端，用于浏览器和 Node.js 环境。它是目前前端开发中最流行的 HTTP 请求库之一。

- 安装
```shell
npm install axios
```
- 导入
```js
import axios from 'axios';
```

## 发送请求

```js
axios({
  method: 'post', // 默认是 get
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  },
  headers: {'X-Requested-With': 'XMLHttpRequest'},
  timeout: 1000,
  responseType: 'json', // 默认
  // 其他配置...
});
```

- 配置
	- `url`:请求的服务器 URL，`String`
	- `method`: `'get', 'post', 'put', 'patch', 'delete'` 等
	- `data`:作为请求体发送的数据，适用于 'post', 'put', 'patch' 方法
	- `params`:与请求一起发送的 URL 参数，自动拼接到 URL 上
	- `headers`:自定义请求头
	- `timeout`:请求超时时间，超时后请求将被中止（ms）
	- 
##  响应结构

```js
{
  data: {}, // 服务器响应数据
  status: 200, // HTTP 状态码
  statusText: 'OK', // 状态消息
  headers: {}, // 响应头
  config: {}, // 请求配置
  request: {} // 生成此响应的请求
}
```
